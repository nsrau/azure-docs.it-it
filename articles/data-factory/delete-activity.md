---
title: Attività Delete in Azure Data Factory | Microsoft Docs
description: Informazioni su come eliminare i file in diversi archivi di file con l'attività Delete in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2019
ms.openlocfilehash: 4d574c0ceef01ed3c292b77b5523b8423d34fe05
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966716"
---
# <a name="delete-activity-in-azure-data-factory"></a>Attività Delete in Azure Data Factory

È possibile usare l'attività Delete in Azure Data Factory per eliminare file o cartelle da archivi locali o cloud. Usare questa attività per pulire o archiviare file quando non sono più necessari.

> [!WARNING]
> Le cartelle o i file eliminati non possono più essere ripristinati. Prestare attenzione quando si usa l'attività Delete per eliminare file o cartelle.

## <a name="best-practices"></a>Procedure consigliate

Ecco alcuni consigli per l'uso dell'attività Delete:

-   Eseguire il backup dei file prima di eliminarli con l'attività Delete nel caso in cui sia necessario ripristinarli in un secondo momento.

-   Assicurarsi che Data Factory abbia le autorizzazioni di scrittura necessarie per eliminare cartelle o file dall'archivio.

-   Assicurarsi di non eliminare file di cui è contemporaneamente in corso la scrittura. 

-   Se si vuole eliminare file o cartelle da un sistema locale, assicurarsi di usare un runtime di integrazione self-hosted con una versione successiva alla 3.13.

## <a name="supported-data-stores"></a>Archivi dati supportati

### <a name="azure-data-stores"></a>Archivi dati di Azure

-   [Archivio BLOB di Azure](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Versione di anteprima di Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)

### <a name="file-system-data-stores"></a>Archivi dati del file system

-   [File system](connector-file-system.md)
-   [FTP](connector-ftp.md)
-   [HDFS](connector-hdfs.md)

## <a name="syntax"></a>Sintassi

```json
{
    "name": "DeleteActivity",
    "type": "Delete",
    "typeProperties": {
        "dataset": {
            "referenceName": "<dataset name to be deleted>",
            "type": "DatasetReference"
        },
        "recursive": true/false,
        "maxConcurrentConnections": <number>,
        "enableLogging": true/false,
        "logStorageSettings": {
            "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference"
            },
            "path": "<path to save log file>"
        }
    }
}
```

## <a name="type-properties"></a>Proprietà del tipo

| Proprietà | Descrizione | Obbligatoria |
| --- | --- | --- |
| dataset | Fornisce il riferimento al set di dati per determinare quali file o cartelle eliminare | Sì |
| ricorsiva | Indica se i file devono essere eliminati in modo ricorsivo dalle sottocartelle o solo dalla cartella specificata.  |  No. Il valore predefinito è `false`. |
| maxConcurrentConnections | Numero di connessioni cui connettere l'archivio simultaneamente per eliminare la cartella o i file.   |   No. Il valore predefinito è `1`. |
| enablelogging | Indica se è necessario registrare i nomi di cartella o di file che sono stati eliminati. Se è true, è necessario fornire un account di archiviazione per salvare il file di log, in modo da poter monitorare i comportamenti dell'attività Delete leggendo il file di log. | No  |
| logStorageSettings | Applicabile solo quando enablelogging = true.<br/><br/>Gruppo di proprietà di archiviazione che può essere specificato e in cui si vuole salvare il file di log che contiene i nomi di cartella o di file eliminati tramite l'attività Delete. | No  |
| linkedServiceName | Applicabile solo quando enablelogging = true.<br/><br/>Servizio collegato di [Archiviazione di Azure](connector-azure-blob-storage.md#linked-service-properties) o [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) in cui archiviare il file di log che contiene i nomi di cartella o di file che sono stati eliminati tramite l'attività Delete. | No  |
| path | Applicabile solo quando enablelogging = true.<br/><br/>Percorso in cui salvare il file di log nell'account di archiviazione. Se non si specifica un percorso, il servizio crea automaticamente un contenitore. | No  |

## <a name="monitoring"></a>Monitoraggio

È possibile monitorare i risultati dell'attività Delete in due posizioni diverse: 
-   Dall'output dell'attività Delete.
-   Dal file di log.

### <a name="sample-output-of-the-delete-activity"></a>Output di esempio dell'attività Delete

```json
{ 
  "isWildcardUsed": false, 
  "wildcard": null,
  "type": "AzureBlobStorage",
  "recursive": true,
  "maxConcurrentConnections": 10,
  "filesDeleted": 1,
  "logPath": "https://sample.blob.core.windows.net/mycontainer/5c698705-a6e2-40bf-911e-e0a927de3f07/5c698705-a6e2-40bf-911e-e0a927de3f07.json",
  "effectiveIntegrationRuntime": "MyAzureIR (West Central US)",
  "executionDuration": 650
}
```

### <a name="sample-log-file-of-the-delete-activity"></a>File di log di esempio dell'attività Delete

```json
{
  "customerInput": {
    "type": "AzureBlob",
    "fileName": "",
    "folderPath": "folder/filename_to_be_deleted",
    "recursive": false,
    "enableFileFilter": false
  },
  "deletedFileList": [
    "folder/filename_to_be_deleted"
  ],
  "deletedFolderList": null,
  "error":"the reason why files are failed to be deleted"
}
```

## <a name="examples-of-using-the-delete-activity"></a>Esempi dell'uso dell'attività Delete

### <a name="delete-specific-folders-or-files"></a>Eliminare cartelle o file specifici

L'archivio ha la struttura di cartelle seguente:

Root/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Cartella_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Cartella_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Cartella_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Cartella_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt

Viene ora usata l'attività Delete per eliminare cartelle o file attraverso la combinazione di valori di proprietà diversi dal set di dati e dall'attività Delete:

| folderPath (dal set di dati) | fileName (dal set di dati) | recursive (dall'attività Delete) | Output |
|:--- |:--- |:--- |:--- |
| Root/Cartella_A_2 | NULL | False | Root/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Cartella_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Cartella_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Cartella_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Cartella_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Root/Cartella_A_2 | NULL | True  | Root/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Cartella_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;<strike>Cartella_A_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Cartella_B_1/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>7.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Cartella_B_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |
| Root/Cartella_A_2 | *.txt | False | Root/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Cartella_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Cartella_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Cartella_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Cartella_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Root/Cartella_A_2 | *.txt | True  | Root/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Cartella_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Cartella_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Cartella_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Cartella_B_2/ |

### <a name="periodically-clean-up-the-time-partitioned-folder-or-files"></a>Pulire periodicamente le cartelle o i file partizionati in base al tempo

È possibile creare una pipeline per pulire periodicamente le cartelle o i file partizionati in base al tempo.  Ad esempio, la struttura di cartelle sarà simile a: `/mycontainer/2018/12/14/*.csv`.  È possibile usare una variabile di sistema di Azure Data Factory dal trigger di pianificazione per identificare le cartelle o i file che devono essere eliminati in ogni esecuzione della pipeline. 

#### <a name="sample-pipeline"></a>Pipeline di esempio

```json
{
    "name": "cleanup_time_partitioned_folder",
    "properties": {
        "activities": [
            {
                "name": "DeleteOneFolder",
                "type": "Delete",
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "typeProperties": {
                    "dataset": {
                        "referenceName": "PartitionedFolder",
                        "type": "DatasetReference",
                        "parameters": {
                            "TriggerTime": {
                                "value": "@formatDateTime(pipeline().parameters.TriggerTime, 'yyyy/MM/dd')",
                                "type": "Expression"
                            }
                        }
                    },
                    "recursive": true,
                    "logStorageSettings": {
                        "linkedServiceName": {
                            "referenceName": "BloblinkedService",
                            "type": "LinkedServiceReference"
                        },
                        "path": "mycontainer/log"
                    },
                    "enableLogging": true
                }
            }
        ],
        "parameters": {
            "TriggerTime": {
                "type": "String"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

#### <a name="sample-dataset"></a>Set di dati di esempio

```json
{
    "name": "PartitionedFolder",
    "properties": {
        "linkedServiceName": {
            "referenceName": "BloblinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "TriggerTime": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@concat('mycontainer/',dataset().TriggerTime)",
                "type": "Expression"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}
```

#### <a name="sample-trigger"></a>Trigger di esempio

```json
{
    "name": "DailyTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "cleanup_time_partitioned_folder",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "TriggerTime": "@trigger().scheduledTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Day",
                "interval": 1,
                "startTime": "2018-12-13T00:00:00.000Z",
                "timeZone": "UTC",
                "schedule": {
                    "minutes": [
                        59
                    ],
                    "hours": [
                        23
                    ]
                }
            }
        }
    }
}
```

### <a name="clean-up-the-expired-files-that-were-last-modified-before-201811"></a>Pulire i file scaduti modificati per l'ultima volta prima della versione 2018.1.1

È possibile creare una pipeline per pulire i file meno recenti o scaduti sfruttando il filtro degli attributi dei file: "LastModified" nel set di dati.  

#### <a name="sample-pipeline"></a>Pipeline di esempio

```json
{
    "name": "CleanupExpiredFiles",
    "properties": {
        "activities": [
            {
                "name": "DeleteFilebyLastModified",
                "type": "Delete",
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "typeProperties": {
                    "dataset": {
                        "referenceName": "BlobFilesLastModifiedBefore201811",
                        "type": "DatasetReference"
                    },
                    "recursive": true,
                    "logStorageSettings": {
                        "linkedServiceName": {
                            "referenceName": "BloblinkedService",
                            "type": "LinkedServiceReference"
                        },
                        "path": "mycontainer/log"
                    },
                    "enableLogging": true
                }
            }
        ]
    }
}
```

#### <a name="sample-dataset"></a>Set di dati di esempio

```json
{
    "name": "BlobFilesLastModifiedBefore201811",
    "properties": {
        "linkedServiceName": {
            "referenceName": "BloblinkedService",
            "type": "LinkedServiceReference"
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": "",
            "folderPath": "mycontainer",
            "modifiedDatetimeEnd": "2018-01-01T00:00:00.000Z"
        }
    }
}
```

### <a name="move-files-by-chaining-the-copy-activity-and-the-delete-activity"></a>Spostare file mediante il concatenamento dell'attività Copy e dell'attività Delete

È possibile spostare un file usando l'attività Copy per copiare un file e quindi l'attività Delete per eliminare un file in una pipeline.  Quando si vuole spostare più file, è possibile usare le attività GetMetadata + Filter + Foreach + Copy + Delete come nell'esempio seguente:

> [!NOTE]
> Se si desidera spostare l'intera cartella definendo un set di dati che contiene solo un percorso di cartella e quindi usando l'attività Copy e l'attività Delete per fare riferimento allo stesso set di dati che rappresenta una cartella, è necessario fare molta attenzione. Il motivo è che è necessario assicurarsi che NON arrivino nuovi file nella cartella tra l'operazione di copia e quella di eliminazione.  Se nuovi file arrivano nella cartella proprio quando l'attività Copy ha completato il processo di copia, ma l'attività Delete non è ancora stata avviata, è possibile che l'attività Delete elimini il file appena arrivato che NON è stato ancora copiato nella destinazione eliminando l'intera cartella. 

#### <a name="sample-pipeline"></a>Pipeline di esempio

```json
{
    "name": "MoveFiles",
    "properties": {
        "activities": [
            {
                "name": "GetFileList",
                "type": "GetMetadata",
                "typeProperties": {
                    "dataset": {
                        "referenceName": "OneSourceFolder",
                        "type": "DatasetReference"
                    },
                    "fieldList": [
                        "childItems"
                    ]
                }
            },
            {
                "name": "FilterFiles",
                "type": "Filter",
                "dependsOn": [
                    {
                        "activity": "GetFileList",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "typeProperties": {
                    "items": {
                        "value": "@activity('GetFileList').output.childItems",
                        "type": "Expression"
                    },
                    "condition": {
                        "value": "@equals(item().type, 'File')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "ForEachFile",
                "type": "ForEach",
                "dependsOn": [
                    {
                        "activity": "FilterFiles",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "typeProperties": {
                    "items": {
                        "value": "@activity('FilterFiles').output.value",
                        "type": "Expression"
                    },
                    "batchCount": 20,
                    "activities": [
                        {
                            "name": "CopyAFile",
                            "type": "Copy",
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": false
                                },
                                "sink": {
                                    "type": "BlobSink"
                                },
                                "enableStaging": false,
                                "dataIntegrationUnits": 0
                            },
                            "inputs": [
                                {
                                    "referenceName": "OneSourceFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "path": "myFolder",
                                        "filename": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "OneDestinationFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "DestinationFileName": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                }
                            ]
                        },
                        {
                            "name": "DeleteAFile",
                            "type": "Delete",
                            "dependsOn": [
                                {
                                    "activity": "CopyAFile",
                                    "dependencyConditions": [
                                        "Succeeded"
                                    ]
                                }
                            ],
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "typeProperties": {
                                "dataset": {
                                    "referenceName": "OneSourceFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "path": "myFolder",
                                        "filename": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                },
                                "logStorageSettings": {
                                    "linkedServiceName": {
                                        "referenceName": "BloblinkedService",
                                        "type": "LinkedServiceReference"
                                    },
                                    "path": "Container/log"
                                },
                                "enableLogging": true
                            }
                        }
                    ]
                }
            }
        ]
    }
}
```

#### <a name="sample-datasets"></a>Set di dati di esempio

Set di dati usato dall'attività GetMetadata per enumerare l'elenco dei file.

```json
{
    "name": "OneSourceFolder",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": "",
            "folderPath": "myFolder"
        }
    }
}
```

Set di dati per l'origine dati usata dall'attività Copy e dall'attività Delete.

```json
{
    "name": "OneSourceFile",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            },
            "filename": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": {
                "value": "@dataset().filename",
                "type": "Expression"
            },
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        }
    }
}
```

Set di dati per la destinazione dei dati usata dall'attività Copy.

```json
{
    "name": "OneDestinationFile",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "DestinationFileName": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": {
                "value": "@dataset().DestinationFileName",
                "type": "Expression"
            },
            "folderPath": "mycontainer/dest"
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla copia di file in Azure Data Factory.

-   [Attività Copy in Azure Data Factory](copy-activity-overview.md)

-   [Strumento Copia dati in Azure Data Factory](copy-data-tool.md)
- 