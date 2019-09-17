---
title: Copiare dati da HDFS usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da un'origine HDFS locale o cloud in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: d8773b330349356410589cf66e8f50636d92b601
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009180"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Copiare dati da HDFS usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-hdfs-connector.md)
> * [Versione corrente](connector-hdfs.md)

Questo articolo illustra come copiare dati da HDFS server. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore HDFS è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

In particolare, il connettore HDFS supporta:

- La copia di file usando l'autenticazione di **Windows** (Kerberos) o **Anonima**.
- La copia di file usando il protocollo **webhdfs** o il supporto per **DistCp integrato**.
- La copia di file così come sono o l'analisi/generazione di file con i [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Assicurarsi che il runtime di integrazione possa accedere a **TUTTI** i [server del nodo dei nomi]: [porta del nodo dei nomi] e [server del nodo dati]: [porta del nodo dati] del cluster Hadoop. La [porta del nodo dei nomi] predefinita è 50070 e la [porta del nodo dati] predefinita è 50075.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per HDFS.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di HDFS sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Hdfs**. | Sì |
| url |URL di HDFS |Sì |
| authenticationType | I valori consentiti sono i seguenti: **Anonymous** o **Windows**. <br><br> Per usare l'**autenticazione Kerberos** per il connettore HDFS, fare riferimento a [questa sezione](#use-kerberos-authentication-for-hdfs-connector) per impostare correttamente l'ambiente locale. |Sì |
| userName |Nome utente per l'autenticazione di Windows Per l'autenticazione Kerberos specificare `<username>@<domain>.com`. |Sì (per l'autenticazione di Windows) |
| password |Password per l'autenticazione di Windows Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì (per l'autenticazione di Windows) |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Ulteriori informazioni sono disponibili nella sezione [prerequisiti](#prerequisites) . Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |N. |

**Esempio: uso dell'autenticazione anonima**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: uso dell'autenticazione di Windows**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). 

- Per **parquet, delimitato testo, JSON, avro e formato binario**, vedere la sezione [parquet, delimitato testo, JSON, avro e formato binario set di dati](#format-based-dataset) .
- Per altri formati come il **formato ORC**, vedere la sezione [altro set di dati del formato](#other-format-dataset) .

### <a name="format-based-dataset"></a>Set di dati parquet, delimitato di testo, JSON, avro e Binary Format

Per copiare dati da **parquet, testo delimitato, JSON, avro e formato binario**, vedere l'articolo formato [parquet](format-parquet.md), [formato testo delimitato](format-delimited-text.md), formato [avro](format-avro.md) e [formato binario](format-binary.md) su DataSet basato su formato e impostazioni supportate. Le proprietà seguenti sono supportate per HDFS `location` in impostazioni nel set di dati basato sul formato:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà `location` Type nel set di dati deve essere impostata su **HdfsLocation**. | Sì      |
| folderPath | Percorso della cartella. Se si vuole usare il carattere jolly per filtrare la cartella, ignorare questa impostazione e specificare nelle impostazioni dell'origine dell'attività. | No       |
| fileName   | Nome del file sotto il folderPath specificato. Se si vuole usare il carattere jolly per filtrare i file, ignorare questa impostazione e specificare nelle impostazioni dell'origine dell'attività. | N.       |

> [!NOTE]
> Il set di dati di tipo **FileShare** con formato parquet/testo indicato nella sezione successiva è ancora supportato così com'è per l'attività di copia/ricerca per la compatibilità con le versioni precedenti. Si consiglia di usare questo nuovo modello in futuro e l'interfaccia utente di creazione di ADF ha cambiato la generazione di questi nuovi tipi.

**Esempio:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Set di dati di altri formati

Per copiare dati da HDFS nel **formato ORC**, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **FileShare** |Sì |
| folderPath | Percorso della cartella. I filtri con caratteri jolly sono supportati, i caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo). Usare `^` per il carattere escape se il nome effettivo del file include caratteri jolly o escape. <br/><br/>Esempi: cartellaradice/sottocartella/. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). |Sì |
| fileName |  **Filtro con nome o carattere jolly** per i file nell'elemento "folderPath" specificato. Se non si specifica alcun valore per questa proprietà, il set di dati punta a tutti i file nella cartella. <br/><br/>Per un filtro, i caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo).<br/>- Esempio 1: `"fileName": "*.csv"`<br/>- Esempio 2: `"fileName": "???20180427.txt"`<br/>Usare `^` per applicare una sequenza di escape se il nome effettivo della cartella include caratteri jolly o tale carattere di escape. |N. |
| modifiedDatetimeStart | Filtro di file basato sull'attributo: Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br/><br/> Tenere presente che le prestazioni complessive dello spostamento dei dati saranno influenzate dall'abilitazione di questa impostazione quando si desidera eseguire il filtro file da grandi quantità di file. <br/><br/> Le proprietà possono essere NULL che significa che al set di dati non verrà applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.| No |
| modifiedDatetimeEnd | Filtro di file basato sull'attributo: Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br/><br/> Tenere presente che le prestazioni complessive dello spostamento dei dati saranno influenzate dall'abilitazione di questa impostazione quando si desidera eseguire il filtro file da grandi quantità di file. <br/><br/> Le proprietà possono essere NULL che significa che al set di dati non verrà applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.| No |
| format | Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si vogliono analizzare file con un formato specifico, sono supportati i tipi di formato file seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](supported-file-formats-and-compression-codecs.md#text-format), [JsonFormat](supported-file-formats-and-compression-codecs.md#json-format), [AvroFormat](supported-file-formats-and-compression-codecs.md#avro-format), [OrcFormat](supported-file-formats-and-compression-codecs.md#orc-format) e [ParquetFormat](supported-file-formats-and-compression-codecs.md#parquet-format). |No (solo per uno scenario di copia binaria) |
| compression | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md#compression-support).<br/>I tipi supportati sono: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono: **Optimal** (Ottimale) e **Fastest** (Più veloce). |No |

>[!TIP]
>Per copiare tutti i file in una cartella, specificare solo **folderPath**.<br>Per copiare un singolo file con un determinato nome, specificare **folderPath** con il percorso della cartella e **fileName** con il nome del file.<br>Per copiare un subset di file in una cartella, specificare **folderPath** con il percorso della cartella e **fileName** con il filtro con caratteri jolly.

**Esempio:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine HDFS.

### <a name="hdfs-as-source"></a>HDFS come origine

- Per eseguire la copia da **parquet, testo delimitato, JSON, avro e formato binario**, vedere la sezione [parquet, delimitato testo, JSON, avro e formato binario](#format-based-source) .
- Per eseguire la copia da altri formati come il **formato ORC**, vedere la sezione [altra origine del formato](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, delimitato testo, JSON, avro e origine del formato binario

Per copiare dati da **parquet, testo delimitato, JSON, avro e formato binario**, vedere l'articolo formato [parquet](format-parquet.md), [formato testo delimitato](format-delimited-text.md), formato [avro](format-avro.md) e [formato binario](format-binary.md) in origine dell'attività di copia basata su formato e supportato Impostazioni. Le proprietà seguenti sono supportate per HDFS `storeSettings` in impostazioni in origine copia basata sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La proprietà Type in `storeSettings` deve essere impostata su **HdfsReadSetting**. | Sì                                           |
| recursive                | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che quando la proprietà recursive è impostata su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. I valori consentiti sono **true** (predefinito) e **false**. | No                                            |
| wildcardFolderPath       | Percorso della cartella con caratteri jolly per filtrare le cartelle di origine. <br>I caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero caratteri o a un carattere singolo). Usare `^` come carattere di escape se il nome effettivo della cartella include caratteri jolly o questo carattere di escape. <br>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | No                                            |
| wildcardFileName         | Nome file con caratteri jolly sotto il folderPath/wildcardFolderPath specificato per filtrare i file di origine. <br>I caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero caratteri o a un carattere singolo). Usare `^` come carattere di escape se il nome effettivo della cartella include caratteri jolly o questo carattere di escape.  Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | Sì se `fileName` non è specificato nel DataSet |
| modifiedDatetimeStart    | Filtro di file basato sull'attributo: Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br> Le proprietà possono essere NULL, a indicare che al set di dati non viene applicato alcun filtro di attributo di file.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime. | No                                            |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                            |
| distcpSettings | Il gruppo di proprietà quando si usa HDFS DistCp. | N. |
| resourceManagerEndpoint | Endpoint Gestione risorse Yarn | Sì, se si usa DistCp |
| tempScriptPath | Un percorso di cartella usato per archiviare lo script di comandi temporaneo per DistCp. Il file di script viene generato da Data Factory e verrà rimosso al termine del processo di copia. | Sì, se si usa DistCp |
| distcpOptions | Opzioni aggiuntive per il comando DistCp. | N. |
| maxConcurrentConnections | Numero di connessioni simultanee per la connessione all'archivio di archiviazione. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | N.                                            |

> [!NOTE]
> Per il formato di testo parquet/delimitato, l'origine dell'attività di copia di tipo **FileSystemSource** citata nella sezione successiva è ancora supportata così com'è per la compatibilità con le versioni precedenti. Si consiglia di usare questo nuovo modello in futuro e l'interfaccia utente di creazione di ADF ha cambiato la generazione di questi nuovi tipi.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSetting",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Altra origine del formato

Per copiare dati da HDFS nel **formato ORC**, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine di attività di copia deve essere impostata su: **HdfsSource** |Sì |
| recursive | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che se recursive è impostata su true e il sink è un archivio basato su file, la cartella o la sottocartella vuota non verrà copiata o creata nel sink.<br/>I valori consentiti sono: **true** (predefinito), **false** | N. |
| distcpSettings | Il gruppo di proprietà quando si usa HDFS DistCp. | N. |
| resourceManagerEndpoint | Endpoint Gestione risorse Yarn | Sì, se si usa DistCp |
| tempScriptPath | Un percorso di cartella usato per archiviare lo script di comandi temporaneo per DistCp. Il file di script viene generato da Data Factory e verrà rimosso al termine del processo di copia. | Sì, se si usa DistCp |
| distcpOptions | Opzioni aggiuntive per il comando DistCp. | N. |
| maxConcurrentConnections | Numero di connessioni simultanee per la connessione all'archivio di archiviazione. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | N. |

**Esempio: Origine HDFS nell'attività di copia mediante DistCp**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

Per altre informazioni su come usare DistCp per copiare in modo efficiente dati da HDFS, vedere la sezione successiva.

### <a name="folder-and-file-filter-examples"></a>Esempi di filtro file e cartelle

Questa sezione descrive il comportamento risultante del percorso cartella e del nome del file con i filtri con caratteri jolly.

| folderPath | fileName             | recursive | Struttura delle cartelle di origine e risultato del filtro (i file in **grassetto** sono stati recuperati) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (vuoto, usare valore predefinito) | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (vuoto, usare valore predefinito) | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Usare DistCp per copiare dati da HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) è uno strumento da riga di comando nativo di Hadoop per eseguire la copia distribuita in un cluster Hadoop. Quando si esegue un comando DistCp, vengono prima elencati tutti i file da copiare e quindi vengono creati vari processi di mapping nel cluster Hadoop, ognuno dei quali esegue la copia binaria dall'origine al sink.

L'attività di copia supporta l'uso di DistCp per copiare i file così come sono nel BLOB di Azure (inclusa la [copia](copy-activity-performance.md)di staging) o in Azure Data Lake Store, nel qual caso è possibile sfruttare appieno la potenza del cluster anziché eseguire la Integration Runtime self-hosted. I miglioramenti in termini di velocità di copia risultano particolarmente significativi se il cluster è molto potente. In base alla configurazione in Azure Data Factory, l'attività di copia crea automaticamente un comando distcp, lo invia al cluster Hadoop e monitorizza lo stato della copia.

### <a name="prerequisites"></a>Prerequisiti

Per usare DistCp per copiare file così come sono da HDFS in BLOB di Azure (inclusa una copia di staging) o in Azure Data Lake Store, assicurarsi che il cluster Hadoop soddisfi i requisiti seguenti:

1. I servizi MapReduce e Yarn siano abilitati.
2. La versione di Yarn sia almeno 2.5.
3. Il server HDFS sia integrato con l'archivio dati di destinazione: BLOB di Azure o Azure Data Lake Store.

    - Il FileSystem di BLOB di Azure è supportato in modo nativo a partire da Hadoop 2.7. È sufficiente specificare il percorso del file con estensione jar nella configurazione dell'ambiente Hadoop.
    - Il FileSystem di Azure Data Lake Store è disponibile come pacchetto a partire da Hadoop 3.0.0-alpha1. Se la versione del cluster Hadoop è inferiore, è necessario importare manualmente nel cluster pacchetti JAR correlati ad Azure Data Lake Store (azure-Lake-store.jar) da [qui](https://hadoop.apache.org/releases.html) e specificare il percorso del file JAR nella configurazione dell'ambiente Hadoop.

4. Preparare una cartella temporanea in HDFS. Questa cartella temporanea viene usata per archiviare lo script della shell DistCp, in modo che occupi solo qualche KB di spazio.
5. Verificare che l'account utente specificato nel servizio collegato di HDFS disponga dell'autorizzazione per a) inviare l'applicazione a Yarn; b) creare una sottocartella, leggere/scrivere file nella cartella temporanea sopra indicata.

### <a name="configurations"></a>Configurazioni

Vedere la sezione configurazioni ed esempi correlati a DistCp nella sezione di [origine HDFS](#hdfs-as-source) .

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Uso dell'autenticazione Kerberos per il connettore HDFS

Sono disponibili due opzioni per configurare l'ambiente locale in modo da poter usare l'autenticazione Kerberos nel connettore HDFS. L'utente può scegliere quella più adatta al caso specifico.
* Opzione 1: [Aggiungere il computer del runtime di integrazione self-hosted nell'area di autenticazione di Kerberos](#kerberos-join-realm)
* Opzione 2: [Abilitare il trust reciproco tra il dominio di Windows e l'area di autenticazione di Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Opzione 1: Aggiungere il computer del runtime di integrazione self-hosted nell'area di autenticazione di Kerberos

#### <a name="requirements"></a>Requisiti

* Il computer del runtime di integrazione self-hosted deve essere aggiunto all'area di autenticazione di Kerberos e non può essere aggiunto a un dominio di Windows.

#### <a name="how-to-configure"></a>Come configurare

**Nel computer del runtime di integrazione self-hosted:**

1.  Eseguire l'utilità **Ksetup** per configurare il server e l'area didi autenticazione di Kerberos KDC.

    Il computer deve essere configurato come membro di un gruppo di lavoro poiché un'area di autenticazione di Kerberos è diversa da un dominio di Windows. È possibile farlo configurando l'area di autenticazione di Kerberos e aggiungendo un server KDC come indicato di seguito. Sostituire *REALM.COM* con la propria area di autenticazione.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Riavviare** il computer dopo aver eseguito questi 2 comandi.

2.  Verificare la configurazione con il comando **Ksetup**. L'output dovrebbe essere simile al seguente:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**In Azure Data Factory:**

* Configurare il connettore HDFS usando **l'autenticazione di Windows** con il nome e la password principali di Kerberos per connettersi all'origine dati HDFS. Controllare la sezione [Proprietà del servizio collegato HDFS](#linked-service-properties) per i dettagli di configurazione.

### <a name="kerberos-mutual-trust"></a>Opzione 2: Abilitare il trust reciproco tra il dominio di Windows e l'area di autenticazione di Kerberos

#### <a name="requirements"></a>Requisiti

*   Il computer del runtime di integrazione self-hosted deve essere aggiunto a un dominio di Windows.
*   È necessaria l'autorizzazione per aggiornare le impostazioni del controller di dominio.

#### <a name="how-to-configure"></a>Come configurare

> [!NOTE]
> Sostituire REALM.COM e AD.COM nell'esercitazione di seguito con le rispettive aree di autenticazione e controller di dominio in base alle esigenze.

**Nel server KDC:**

1. Modificare la configurazione KDC nel file **krb5.conf** per far considerare attendibile a KDC il dominio di Windows che fa riferimento al modello di configurazione di seguito. Per impostazione predefinita, la configurazione si trova in **/etc/krb5.conf**.

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log
            
           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true
            
           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }
            
           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM
            
           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   **Riavviare** il servizio KDC dopo la configurazione.

2. Preparare un'entità denominata **krbtgt/Realm. com\@ad.com** nel server KDC con il comando seguente:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. Nel file di configurazione del servizio HDFS **hadoop.security.auth_to_local** aggiungere `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**Nel controller di dominio:**

1.  Eseguire i comandi **Ksetup** seguenti per aggiungere una voce di area di autenticazione:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Stabilire una relazione di trust dal dominio di Windows all'area di autenticazione di Kerberos. [password] è la password per l'entità **krbtgt/Realm. com\@ad.com**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Selezionare l'algoritmo di crittografia usato in Kerberos.

    1. Passare a Server Manager > Gestione Criteri di gruppo > Dominio > Oggetti Criteri di gruppo > Criteri dominio attivi o predefiniti e selezionare Modifica.

    2. Nella finestra popup **Editor Gestione criteri di gruppo**, passare a Configurazione computer > Criteri > Impostazioni di Windows > Impostazioni di sicurezza > Criteri locali > Opzioni di sicurezza e configurare **Sicurezza di rete: configura tipi di crittografia consentiti per Kerberos**.

    3. Selezionare l'algoritmo di crittografia da usare per connettersi a KDC. In genere, è possibile selezionare semplicemente tutte le opzioni.

        ![Configurare i tipi di crittografia per Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Utilizzare il comando **Ksetup** per specificare l'algoritmo di crittografia da usare nell'area di autenticazione specifica.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Creare il mapping tra l'account di dominio e l'entità Kerberos per poter usare quest'ultima nel dominio di Windows.

    1. Avviare Strumenti di amministrazione > **Utenti e computer di Active Directory**.

    2. Configurare funzionalità avanzate facendo clic su **Visualizza** > **Funzionalità avanzate**.

    3. Individuare l'account per cui si desidera creare i mapping e fare clic con il tasto destor del mouse per visualizzare **Mapping nomi**. Fare clic sulla scheda **Nomi Kerberos**.

    4. Aggiungere un'entità dall'area di autenticazione.

        ![Eseguire il mapping di sicurezza e identità](media/connector-hdfs/map-security-identity.png)

**Nel computer del runtime di integrazione self-hosted:**

* Eseguire i comandi **Ksetup** seguenti per aggiungere una voce di area di autenticazione.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**In Azure Data Factory:**

* Configurare il connettore HDFS usando **l'autenticazione di Windows** con l'account di dominio o l'entità Kerberos per connettersi all'origine dati HDFS. Controllare la sezione [Proprietà del servizio collegato HDFS](#linked-service-properties) per i dettagli di configurazione.

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
