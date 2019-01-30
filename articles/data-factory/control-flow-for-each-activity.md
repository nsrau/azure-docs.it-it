---
title: Attività ForEach in Azure Data Factory | Microsoft Docs
description: L'attività ForEach definisce un flusso di controllo ripetuto nella pipeline. Viene usata per eseguire l'iterazione di una raccolta e attività specifiche.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: shlo
ms.openlocfilehash: 68cdabd8d6e5921eabaa200169c0523352461733
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856945"
---
# <a name="foreach-activity-in-azure-data-factory"></a>Attività ForEach in Azure Data Factory
L'attività ForEach definisce un flusso di controllo ripetuto nella pipeline. Questa attività viene usata per eseguire l'iterazione di una raccolta e attività specifiche in un ciclo. L'implementazione di cicli di questa attività è simile alla struttura di esecuzione in ciclo Foreach nei linguaggi di programmazione.

## <a name="syntax"></a>Sintassi
Le proprietà sono descritte più avanti in questo articolo. La proprietà items rappresenta la raccolta e ogni elemento della raccolta viene definito tramite `@item()`, come illustrato nella sintassi seguente:  

```json
{  
   "name":"MyForEachActivityName",
   "type":"ForEach",
   "typeProperties":{  
      "isSequential":"true",
        "items": {
            "value": "@pipeline().parameters.mySinkDatasetFolderPathCollection",
            "type": "Expression"
        },
      "activities":[  
         {  
            "name":"MyCopyActivity",
            "type":"Copy",
            "typeProperties":{  
               ...
            },
            "inputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@pipeline().parameters.mySourceDatasetFolderPath"
                  }
               }
            ],
            "outputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@item()"
                  }
               }
            ]
         }
      ]
   }
}

```

## <a name="type-properties"></a>Proprietà del tipo

Proprietà | DESCRIZIONE | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
name | Nome dell'attività ForEach. | string | Yes
type | Deve essere impostato su **ForEach** | string | Yes
isSequential | Specifica se il ciclo deve essere eseguito in sequenza o in parallelo.  È possibile eseguire un numero massimo di 20 iterazioni del ciclo simultanee in parallelo. Se si dispone ad esempio di un'iterazione di attività ForEach su un'attività di copia con 10 set di dati di origine e sink diversi con **isSequential** impostato su False, tutte le copie vengono eseguite simultaneamente. Il valore predefinito è False. <br/><br/> Se "isSequential" è impostato su False, assicurarsi che sia presente una configurazione corretta per usare più eseguibili. In caso contrario, questa proprietà deve essere usata con attenzione per evitare di incorrere in conflitti di scrittura. Per altre informazioni, vedere la sezione [Esecuzione parallela](#parallel-execution). | Boolean |  No. Il valore predefinito è False.
batchCount | Numero di batch da usare per controllare il numero di esecuzione parallela (quando isSequential è impostato su Falso). | Valore intero (massimo 50) |  No. Il valore predefinito è 20.
Items | Un'espressione che restituisce una matrice JSON su cui eseguire un'iterazione. | Espressione (che restituisce una matrice JSON) | Yes
attività | Le attività da eseguire. | Elenco di attività | Yes

## <a name="parallel-execution"></a>Esecuzione parallela
Se **isSequential** è impostato su false, l'attività esegue le iterazioni in parallelo con un massimo di 20 iterazioni simultanee. Questa impostazione deve essere usata con cautela. Se le iterazioni simultanee scrivono nella stessa cartella, ma in file diversi, non ci sono problemi. Se le iterazioni simultanee scrivono contemporaneamente in esattamente lo stesso file, questo approccio causa un errore. 

## <a name="iteration-expression-language"></a>Linguaggio delle espressioni di iterazione
Nell'attività ForEach, fornire una matrice di cui eseguire un'iterazione per la proprietà **items**." Usare `@item()` per eseguire l'iterazione su un'unica enumerazione nell'attività ForEach. Ad esempio, se **items** è una matrice: [1, 2, 3], `@item()` restituisce 1 nella prima iterazione, 2 nella seconda iterazione e 3 nella terza iterazione.

## <a name="iterating-over-a-single-activity"></a>Iterazione su una singola attività
**Scenario:** copia dello stesso file di origine in BLOB di Azure in più file di destinazione nel BLOB di Azure.

### <a name="pipeline-definition"></a>Definizione della pipeline

```json
{
    "name": "<MyForEachPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyForEachActivity>",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "true",
                    "items": {
                        "value": "@pipeline().parameters.mySinkDatasetFolderPath",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "MyCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": "false"
                                },
                                "sink": {
                                    "type": "BlobSink",
                                    "copyBehavior": "PreserveHierarchy"
                                }
                            },
                            "inputs": [
                                {
                                    "referenceName": "<MyDataset>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@pipeline().parameters.mySourceDatasetFolderPath"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "MyDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@item()"
                                    }
                                }
                            ]
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "mySourceDatasetFolderPath": {
                "type": "String"
            },
            "mySinkDatasetFolderPath": {
                "type": "String"
            }
        }
    }
}

```

### <a name="blob-dataset-definition"></a>Definizione del set di dati BLOB

```json
{  
   "name":"<MyDataset>",
   "properties":{  
      "type":"AzureBlob",
      "typeProperties":{  
         "folderPath":{  
            "value":"@dataset().MyFolderPath",
            "type":"Expression"
         }
      },
      "linkedServiceName":{  
         "referenceName":"StorageLinkedService",
         "type":"LinkedServiceReference"
      },
      "parameters":{  
         "MyFolderPath":{  
            "type":"String"
         }
      }
   }
}

```

### <a name="run-parameter-values"></a>Valori del parametro di esecuzione

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Eseguire l'iterazione su più attività
È possibile eseguire l'iterazione su più attività (ad esempio attività di copia e web) in un'attività ForEach. In questo scenario, si consiglia di astrarre più attività in una pipeline distinta. È quindi possibile usare l'[attività ExecutePipeline](control-flow-execute-pipeline-activity.md) nella pipeline con l'attività ForEach per richiamare la pipeline distinta con più attività. 


### <a name="syntax"></a>Sintassi

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "<MyForEachMultipleActivities>"
        "typeProperties": {
          "isSequential": true,
          "items": {
            ...
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "name": "<MyInnerPipeline>"
              "typeProperties": {
                "pipeline": {
                  "referenceName": "<copyHttpPipeline>",
                  "type": "PipelineReference"
                },
                "parameters": {
                  ...
                },
                "waitOnCompletion": true
              }
            }
          ]
        }
      }
    ],
    "parameters": {
      ...
    }
  }
}

```

### <a name="example"></a>Esempio
**Scenario:** iterazione di una pipeline interna in un'attività ForEach con l'attività ExecutePipeline. La pipeline interna viene copiata con le definizioni dello schema parametrizzate.

#### <a name="master-pipeline-definition"></a>Definizione della pipeline master

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "MyForEachActivity",
        "typeProperties": {
          "isSequential": true,
          "items": {
            "value": "@pipeline().parameters.inputtables",
            "type": "Expression"
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "typeProperties": {
                "pipeline": {
                  "referenceName": "InnerCopyPipeline",
                  "type": "PipelineReference"
                },
                "parameters": {
                  "sourceTableName": {
                    "value": "@item().SourceTable",
                    "type": "Expression"
                  },
                  "sourceTableStructure": {
                    "value": "@item().SourceTableStructure",
                    "type": "Expression"
                  },
                  "sinkTableName": {
                    "value": "@item().DestTable",
                    "type": "Expression"
                  },
                  "sinkTableStructure": {
                    "value": "@item().DestTableStructure",
                    "type": "Expression"
                  }
                },
                "waitOnCompletion": true
              },
              "name": "ExecuteCopyPipeline"
            }
          ]
        }
      }
    ],
    "parameters": {
      "inputtables": {
        "type": "Array"
      }
    }
  }
}

```

#### <a name="inner-pipeline-definition"></a>Definizione della pipeline interna

```json
{
  "name": "InnerCopyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            }
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "name": "CopyActivity",
        "inputs": [
          {
            "referenceName": "sqlSourceDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sourceTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sourceTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sqlSinkDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sinkTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sinkTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceTableName": {
        "type": "String"
      },
      "sourceTableStructure": {
        "type": "String"
      },
      "sinkTableName": {
        "type": "String"
      },
      "sinkTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="source-dataset-definition"></a>Definizione del set di dati di origine

```json
{
  "name": "sqlSourceDataset",
  "properties": {
    "type": "SqlServerTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "sqlserverLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="sink-dataset-definition"></a>Definizione del set di dati sink

```json
{
  "name": "sqlSinkDataSet",
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "azureSqlLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="master-pipeline-parameters"></a>Parametri della pipeline master
```json
{
    "inputtables": [
        {
            "SourceTable": "department",
            "SourceTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ],
            "DestTable": "department2",
            "DestTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ]
        }
    ]
    
}
```

## <a name="aggregating-outputs"></a>Aggregazione di output

Per aggregare gli output dell'attività __foreach__, usare _Variable_s e l'attività _Aggiungi variabile_.

Innanzitutto, dichiarare una `array` _variabile_ nella pipeline. Quindi, richiamare l'attività _Aggiungi variabile_ all'interno di ogni ciclo __foreach__. Successivamente, è possibile recuperare l'aggregazione dall'array.

## <a name="limitations-and-workarounds"></a>Limitazioni e soluzioni alternative

Di seguito vengono descritte alcune limitazioni dell'attività ForEach con le soluzioni alternative suggerite.

| Limitazione | Soluzione alternativa |
|---|---|
| Non è possibile annidare un ciclo ForEach all'interno di un altro ciclo ForEach (o un ciclo Until). | Progettare una pipeline a due livelli, in cui la pipeline esterna con il ciclo ForEach esterno esegue l'iterazione su una pipeline interna con il ciclo annidato. |
| Per l'attività ForEach sono previsti un `batchCount` massimo di 50 per l'elaborazione parallela e un massimo di 100.000 elementi. | Progettare una pipeline a due livelli in cui la pipeline esterna con l'attività ForEach esegue l'iterazione su una pipeline interna. |
| | |

## <a name="next-steps"></a>Passaggi successivi
Vedere altre attività del flusso di controllo supportate da Data Factory: 

- [Attività ExecutePipeline](control-flow-execute-pipeline-activity.md)
- [Attività Get Metadata](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
