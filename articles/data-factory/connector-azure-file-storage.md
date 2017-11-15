---
title: Copiare dati da o ad Archiviazione file di Azure tramite Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da Archiviazione file di Azure ad archivi dati sink supportati o da archivi dati di origine supportati ad Archiviazione file di Azure tramite Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: jingwang
ms.openlocfilehash: edbab30d949daa8d564ec60e9f1650f38b01d942
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-or-to-azure-file-storage-by-using-azure-data-factory"></a>Copiare dati da o ad Archiviazione file di Azure tramite Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e ad Archiviazione file di Azure (File di Azure). Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da Archiviazione file di Azure a un qualsiasi archivio dati sink supportato o da qualsiasi archivio dati di origine ad Archiviazione file di Azure. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Archiviazione file di Azure supporta la copia dei file così come sono e l'analisi o la generazione di file con i [formati di file e i codec di compressione supportati](supported-file-formats-and-compression-codecs.md).

## <a name="getting-started"></a>introduttiva

È possibile creare una pipeline con l'attività di copia usando .NET SDK, Python SDK, Azure PowerShell, l'API REST o il modello Azure Resource Manager. Vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Le sezioni seguenti offrono informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche per Archiviazione file di Azure.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Archiviazione file di Azure sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **FileServer**. | Sì |
| host | Specifica l'endpoint di Archiviazione file di Azure come `"host": "\\\\<storage name>.file.core.windows.net\\<file service name>"`. | Sì |
| userid | Specificare l'utente con cui accedere ad Archiviazione file di Azure come `"userid": "AZURE\\<storage name>"`. | Sì |
| password | Specificare la chiave di accesso alle risorse di archiviazione. Contrassegnare questo campo come SecureString.<br/> | Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

>[!TIP]
> - Per copiare in Archiviazione file di Azure tramite Azure Integration Runtime, [creare un'istanza di Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) in modo esplicito con la posizione di Archiviazione file ed eseguire l'associazione nel servizio collegato come nell'esempio seguente.
> - Per copiare da/ad Archiviazione file di Azure tramite Integration Runtime (self-hosted) all'esterno di Azure, è necessario aprire la porta TCP 445 in uscita nella rete locale.

**Esempio:**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "FileServer",
        "typeProperties": {
            "host": "\\\\<storage name>.file.core.windows.net\\<file service name>",
            "userid": "AZURE\\<storage name>",
            "password": {
                "type": "SecureString",
                "value": "<storage access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco di proprietà supportate dal set di dati di Archiviazione file di Azure.

Per copiare dati da/ad Archiviazione file di Azure, impostare la proprietà type del set di dati su **FileShare**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **FileShare** |Sì |
| folderPath | Percorso della cartella. |Sì |
| fileName | Se si vuole copiare da/in un file specifico, specificare il nome del file in **folderPath**. Se non si specifica alcun valore per questa proprietà, il set di dati seleziona tutti i file nella cartella come origine e genera automaticamente il nome del file.<br/><br/>**Generazione automatica di fileName per il sink:** se fileName non è specificato per un set di dati di output e **preserveHierarchy** non è specificato nel sink dell'attività, l'attività di copia genera il nome del file con il formato seguente: <br/>- `Data_[activity run id]_[GUID].[format].[compression if configured]`. Ad esempio: `Data_0a405f8a-93ff-4c6f-b3be-f69616f1df7a_0d143eda-d5b8-44df-82ec-95c50895ff80.txt.gz` <br/>- o `[Table name].[format].[compression if configured]` per l'origine relazionale se non è specificata la query. Ad esempio: MySourceTable.orc. |No |
| fileFilter | Specificare un filtro da usare per selezionare un sottoinsieme di file in folderPath anziché tutti i file. Si applica solo se fileName non è specificato. <br/><br/>I valori jolly consentiti sono: `*` (più caratteri) e `?` (carattere singolo).<br/>- Esempio 1: `"fileFilter": "*.log"`<br/>- Esempio 2: `"fileFilter": 2017-09-??.txt"` |No |
| format | Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si vuole analizzare o generare file con un formato specifico, sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](supported-file-formats-and-compression-codecs.md#text-format), [JsonFormat](supported-file-formats-and-compression-codecs.md#json-format), [AvroFormat](supported-file-formats-and-compression-codecs.md#avro-format), [OrcFormat](supported-file-formats-and-compression-codecs.md#orc-format) e [ParquetFormat](supported-file-formats-and-compression-codecs.md#parquet-format). |No (solo per uno scenario di copia binaria) |
| compressione | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md#compression-support).<br/>I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono **Ottimale** e **Più veloce**. |No |

**Esempio:**

```json
{
    "name": "AzureFileStorageDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<Azure File Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Archiviazione file di Azure.

### <a name="azure-file-storage-as-source"></a>Archiviazione file di Azure come origine

Per copiare dati da Archiviazione file di Azure, impostare il tipo di origine nell'attività di copia su **FileSystemSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **FileSystemSource** |Sì |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata.<br/>I valori consentiti sono: **true** (predefinito), **false** | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure File Storage input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-file-storage-as-sink"></a>Archiviazione file di Azure come sink

Per copiare dati da Archiviazione file di Azure, impostare il tipo di sink nell'attività di copia su **FileSystemSink**. Nella sezione **sink** sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su: **FileSystemSink** |Sì |
| copyBehavior | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>- PreserveHierarchy (predefinito)</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. Il nome dei file di destinazione viene generato automaticamente. <br/><b>- MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se viene specificato il nome file/BLOB, il nome file unito sarà il nome specificato. In caso contrario, sarà il nome file generato automaticamente. | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure File Storage output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "FileSystemSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="recursive-and-copybehavior-examples"></a>esempi ricorsivi e copyBehavior

In questa sezione viene descritto il comportamento derivante dell'operazione di copia per diverse combinazioni di valori ricorsivi e copyBehavior.

| ricorsiva | copyBehavior | Struttura della cartella di origine | Destinazione risultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La cartella di destinazione Cartella1 viene creata con la stessa struttura dell'origine:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5. |
| true |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File5 |
| true |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 + File3 + File4 + File 5 viene unito in un file con nome file generato automaticamente |
| false |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Sottocartella1 con File3, File4 e File5 non considerati. |
| false |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File2<br/><br/>Sottocartella1 con File3, File4 e File5 non considerati. |
| false |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 viene unito in un file con un nome file generato automaticamente. Nome generato automaticamente per File1<br/><br/>Sottocartella1 con File3, File4 e File5 non considerati. |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).