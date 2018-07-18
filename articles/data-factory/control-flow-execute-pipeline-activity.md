---
title: Attività ExecutePipeline in Azure Data Factory | Microsoft Docs
description: Informazioni su come usare l'attività ExecutePipeline per richiamare una pipeline di Data Factory da un'altra pipeline di Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 2aa25004fb9c2e914cd8c669095953e174686197
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051764"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Attività ExecutePipeline in Azure Data Factory
L'attività ExecutePipeline consente a una pipeline di Data Factory di richiamare un'altra pipeline.

## <a name="syntax"></a>Sintassi

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>Proprietà del tipo
Proprietà | DESCRIZIONE | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
name | Nome dell'attività di esecuzione della pipeline. | string | Sì
type | Deve essere impostata su: **ExecutePipeline**. | string | Sì
pipeline | Pipeline di riferimento per la pipeline dipendente chiamata da questa pipeline. Un oggetto di riferimento della pipeline ha due proprietà: **referenceName** e **type**. La proprietà referenceName specifica il nome della pipeline di riferimento. La proprietà type deve essere impostata su PipelineReference. | PipelineReference | Sì
Parametri | Parametri da passare alla pipeline chiamata | Oggetto JSON che esegue il mapping dei nomi di parametro ai valori degli argomenti | No 
waitOnCompletion | Definisce se l'esecuzione dell'attività attende che l'esecuzione della pipeline dipendente termini. | Il valore predefinito è false. | boolean | No 

## <a name="sample"></a>Esempio
Questo scenario include due pipeline:

- **Pipeline master**: questa pipeline ha una sola attività di esecuzione della pipeline che chiama la pipeline richiamata. La pipeline master accetta due parametri: `masterSourceBlobContainer` e `masterSinkBlobContainer`.
- **Pipeline richiamata**: questa pipeline ha un'attività Copy che copia i dati da un'origine BLOB di Azure al sink di BLOB di Azure. La pipeline richiamata accetta due parametri: `sourceBlobContainer`, `sinkBlobContainer`.

### <a name="master-pipeline-definition"></a>Definizione della pipeline master

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobCountainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>Definizione della pipeline richiamata

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

**Servizio collegato**

```json
{
    "name": "BlobStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=*****",
        "type": "SecureString"
      }
    }
  }
}
```

**Set di dati di origine**
```json
{
    "name": "SourceBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sourceBlobContainer",
        "type": "Expression"
      },
      "fileName": "salesforce.txt"
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

**Set di dati sink**
```json
{
    "name": "sinkBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sinkBlobContainer",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

### <a name="running-the-pipeline"></a>Esecuzione della pipeline

Per eseguire la pipeline master di questo esempio, vengono passati i valori seguenti per i parametri masterSourceBlobContainer e masterSinkBlobContainer: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

La pipeline master inoltra questi valori alla pipeline richiamata, come illustrato nell'esempio seguente: 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobCountainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>Passaggi successivi
Vedere altre attività del flusso di controllo supportate da Data Factory: 

- [Attività ForEach](control-flow-for-each-activity.md)
- [Attività Get Metadata](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
