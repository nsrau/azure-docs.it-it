---
title: Pipeline e attività in Azure Data Factory
description: Informazioni sulle pipeline e le attività in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 93d741d22ac03c132954a48731451f891042d7b4
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371172"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipeline e attività in Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-create-pipelines.md)
> * [Versione corrente](concepts-pipelines-activities.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo fornisce informazioni sulle pipeline e sulle attività in Azure Data Factory e su come usarle per creare flussi di lavoro completi basati sui dati per gli scenari di elaborazione e trasferimento dei dati.

## <a name="overview"></a>Panoramica
Una data factory può comprendere una o più pipeline. Una pipeline è un raggruppamento logico di attività che insieme eseguono un'operazione. Una pipeline, ad esempio, può contenere un set di attività che inseriscono e puliscono i dati di log e quindi avviano un flusso di dati di mapping per analizzare i dati di log. La pipeline consente di gestire le attività come un set anziché singolarmente. Si distribuisce e si pianifica la pipeline anziché ogni attività in modo indipendente.

Le attività in una pipeline definiscono le azioni da eseguire sui dati. Ad esempio, è possibile usare un'attività di copia per copiare i dati da SQL Server in un archivio BLOB di Azure. Quindi, usare un'attività flusso di dati o un'attività del notebook di databricks per elaborare e trasformare i dati dall'archiviazione BLOB a un pool di analisi delle sinapsi di Azure in cui vengono compilate business intelligence soluzioni di creazione di report.

Data Factory prevede tre raggruppamenti di attività: [attività di spostamento dei dati](copy-activity-overview.md), [attività di trasformazione dei dati](transform-data.md)e [attività di controllo](#control-flow-activities). Un'attività può assumere zero o più [set](concepts-datasets-linked-services.md) di dati di input e generare uno o più [set](concepts-datasets-linked-services.md)di dati di output. Nel diagramma seguente viene illustrata la relazione tra attività, set di dati e pipeline in Data Factory:

![Relazione tra set di dati, attività e pipeline](media/concepts-pipelines-activities/relationship-between-dataset-pipeline-activity.png)

Un set di dati di input rappresenta l'input per un'attività nella pipeline, un set di dati di output rappresenta l'output dell'attività. I set di dati identificano i dati all'interno dei diversi archivi dati, come tabelle, file, cartelle e documenti. Dopo aver creato un set di dati, è possibile usarlo con le attività in una pipeline. Ad esempio, un set di dati può essere configurato come set di dati di input o di output di un'attività di copia o un'attività HDInsightHive. Per altre informazioni sui set di dati, vedere l'articolo [Set di dati in Azure Data Factory](concepts-datasets-linked-services.md).

## <a name="data-movement-activities"></a>Attività di spostamento dei dati

L'attività di copia in Data Factory esegue la copia dei dati da un archivio dati di origine a un archivio dati sink. Il servizio Data Factory supporta gli archivi dati elencati nella tabella in questa sezione. I dati da qualsiasi origine possono essere scritti in qualsiasi sink. Fare clic su un archivio dati per informazioni su come copiare dati da e verso tale archivio.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

Per altre informazioni, vedere l'articolo [Copy Activity in Azure Data Factory](copy-activity-overview.md) (Attività di copia in Azure Data Factory).

## <a name="data-transformation-activities"></a>Attività di trasformazione dei dati
Azure Data Factory supporta le seguenti attività di trasformazione che possono essere aggiunte alle pipeline singolarmente o con un'altra attività concatenata.

Attività di trasformazione dei dati | Ambiente di calcolo
---------------------------- | -------------------
[Flusso di dati](control-flow-execute-data-flow-activity.md) | Azure Databricks gestiti da Azure Data Factory
[Funzione di Azure](control-flow-azure-function-activity.md) | Funzioni di Azure
[Hive](transform-data-using-hadoop-hive.md) | HDInsight [Hadoop]
[Pig](transform-data-using-hadoop-pig.md) | HDInsight [Hadoop]
[MapReduce](transform-data-using-hadoop-map-reduce.md) | HDInsight [Hadoop]
[Hadoop Streaming](transform-data-using-hadoop-streaming.md) | HDInsight [Hadoop]
[Spark](transform-data-using-spark.md) | HDInsight [Hadoop]
[Attività Azure Machine Learning Studio (classiche): esecuzione batch e risorsa di aggiornamento](transform-data-using-machine-learning.md) | Macchina virtuale di Azure
[Stored procedure](transform-data-using-stored-procedure.md) | Azure SQL, Azure sinapsi Analytics (in precedenza SQL Data Warehouse) o SQL Server
[U-SQL](transform-data-using-data-lake-analytics.md) | Azure Data Lake Analytics.
[Attività personalizzata](transform-data-using-dotnet-custom-activity.md) | Azure Batch
[Notebook di Databricks](transform-data-databricks-notebook.md) | Azure Databricks
[Attività JAR di Databricks](transform-data-databricks-jar.md) | Azure Databricks
[Attività Python di Databricks](transform-data-databricks-python.md) | Azure Databricks

Per altre informazioni, vedere l'articolo [Attività di trasformazione dei dati](transform-data.md).

## <a name="control-flow-activities"></a>Attività flusso di controllo
Sono supportate le seguenti attività del flusso di controllo:

Attività di controllo | Descrizione
---------------- | -----------
[Aggiungi variabile](control-flow-append-variable-activity.md) | Aggiungere un valore a una variabile di matrice esistente.
[Esegui pipeline](control-flow-execute-pipeline-activity.md) | L'attività di esecuzione pipeline consente a una pipeline di Data Factory di richiamare un'altra pipeline.
[Filter](control-flow-filter-activity.md) | Applicare un'espressione di filtro a una matrice di input
[Per ogni](control-flow-for-each-activity.md) | L'attività ForEach definisce un flusso di controllo ripetuto nella pipeline. Questa attività viene usata per eseguire l'iterazione di una raccolta e attività specifiche in un ciclo. L'implementazione in cicli di questa attività è simile alla struttura di esecuzione in cicli Foreach nei linguaggi di programmazione.
[Ottenere metadati](control-flow-get-metadata-activity.md) | Questa attività può essere usata per recuperare i metadati di tutti i dati in Azure Data Factory.
[Attività della condizione If](control-flow-if-condition-activity.md) | Può essere usata per creare un ramo in base alla condizione che il valore restituito sia true o false. L'attività IfCondition svolge la stessa funzione dell'istruzione If nei linguaggi di programmazione. Valuta un set di attività quando la condizione restituisce `true` e un altro set di attività quando la condizione restituisce `false.`
[Attività Lookup](control-flow-lookup-activity.md) | L'attività Lookup può essere usata per la lettura o la ricerca di un record/nome di tabella/valore da qualsiasi origine esterna. Questo output può essere referenziato ulteriormente dalle attività successive.
[Imposta variabile](control-flow-set-variable-activity.md) | Impostare il valore di una variabile esistente.
[Attività Until](control-flow-until-activity.md) | Implementa il ciclo Do-Until che è simile alla struttura di esecuzione cicli Do-Until nei linguaggi di programmazione. Esegue infatti un set di attività in un ciclo finché la condizione associata con l'attività restituisce true. È possibile specificare un valore di timeout per l'attività Until in Data Factory.
[Attività Validation](control-flow-validation-activity.md) | Verificare che una pipeline continui l'esecuzione solo se esiste un set di dati di riferimento, soddisfa i criteri specificati oppure è stato raggiunto un timeout.
[Attività Wait](control-flow-wait-activity.md) | Quando si usa un'attività Wait in una pipeline, la pipeline attende il tempo specificato prima di continuare con l'esecuzione delle attività successive.
[Attività Web](control-flow-web-activity.md) | L'attività Web può essere usata per chiamare un endpoint REST personalizzato da una pipeline di Data Factory. È possibile passare set di dati e servizi collegati in modo che l'attività possa usarli e accedervi.
[Attività Webhook](control-flow-webhook-activity.md) | Usando l'attività webhook, chiamare un endpoint e passare un URL di callback. L'esecuzione della pipeline attende che il callback venga richiamato prima di procedere all'attività successiva.

## <a name="pipeline-json"></a>Pipeline JSON
Ecco come una pipeline viene definita in formato JSON:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities":
        [
        ],
        "parameters": {
        },
        "concurrency": <your max pipeline concurrency>,
        "annotations": [
        ]
    }
}
```

Tag | Descrizione | Type | Obbligatoria
--- | ----------- | ---- | --------
name | Nome della pipeline. Specificare un nome che rappresenti l'azione eseguita dalla pipeline. <br/><ul><li>Numero massimo di caratteri: 140</li><li>Deve iniziare con una lettera, numero o un carattere di sottolineatura (\_)</li><li>Non sono consentiti i caratteri seguenti: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \" </li></ul> | string | Sì
description | Specificare il testo descrittivo che illustra lo scopo della pipeline. | string | No
attività | Nella sezione delle **attività** possono essere definite una o più attività. Vedere la sezione relativa al formato [JSON delle attività](#activity-json) per informazioni dettagliate sull'elemento JSON delle attività. | Array | Sì
parametri | La sezione **parameters** può avere uno o più parametri definiti all'interno della pipeline, assicurando la flessibilità per il riutilizzo della pipeline. | Elenco | No
Concorrenza | Numero massimo di esecuzioni simultanee consentite dalla pipeline. Per impostazione predefinita, non esiste alcun valore massimo. Se viene raggiunto il limite di concorrenza, le esecuzioni di pipeline aggiuntive vengono accodate fino al completamento di quelle precedenti | Number | No 
annotations | Elenco di tag associati alla pipeline | Array | No

## <a name="activity-json"></a>Attività JSON
Nella sezione delle **attività** possono essere definite una o più attività. Esistono due tipi principali di attività: attività di esecuzione e di controllo.

### <a name="execution-activities"></a>Attività di esecuzione
Includono le [attività di spostamento dei dati](#data-movement-activities) e di [ trasformazione dei dati](#data-transformation-activities). Presentano la seguente struttura di primo livello:

```json
{
    "name": "Execution Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "linkedServiceName": "MyLinkedService",
    "policy":
    {
    },
    "dependsOn":
    {
    }
}
```

La tabella seguente descrive le proprietà all'interno della definizione JSON dell'attività:

Tag | Descrizione | Obbligatoria
--- | ----------- | ---------
name | Nome dell'attività. Specificare un nome che rappresenti l'azione eseguita dall'attività. <br/><ul><li>Numero massimo di caratteri: 55</li><li>Deve iniziare con una lettera, un numero o un carattere di sottolineatura ( \_ )</li><li>Non sono consentiti i caratteri seguenti: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \" | Sì</li></ul>
description | Testo descrittivo per il tipo o lo scopo dell'attività | Sì
type | Tipo di attività. Per informazioni sui diversi tipi di attività, vedere le sezioni [Attività di spostamento dei dati](#data-movement-activities), [Attività di trasformazione dei dati](#data-transformation-activities) e [Attività di controllo](#control-flow-activities). | Sì
linkedServiceName | Nome del servizio collegato usato dall'attività.<br/><br/>Per un'attività può essere necessario specificare il servizio collegato che collega all'ambiente di calcolo richiesto. | Sì per attività HDInsight, attività di assegnazione di punteggi batch Azure Machine Learning Studio (classica), attività stored procedure. <br/><br/>No per tutto il resto
typeProperties | Le proprietà nella sezione typeProperties dipendono da ogni tipo di attività. Per visualizzare le proprietà del tipo per un'attività, fare clic sui collegamenti all'attività nella sezione precedente. | No
Criterio | Criteri che influiscono sul comportamento di runtime dell'attività. Questa proprietà include un comportamento di timeout e ripetizione dei tentativi. Se non è specificato, vengono utilizzati i valori predefiniti. Per altre informazioni, vedere la sezione [Criteri di attività](#activity-policy). | No
dependsOn | Questa proprietà viene usata per definire le dipendenze delle attività e come le attività successive dipendono dalle attività precedenti. Per altre informazioni, vedere [Dipendenza delle attività](#activity-dependency). | No

### <a name="activity-policy"></a>Criteri di attività
Criteri che influiscono sul comportamento runtime di un'attività, offrendo le opzioni di configurabilità. I criteri delle attività sono disponibili solo per le attività di esecuzione.

### <a name="activity-policy-json-definition"></a>Definizione JSON dei criteri di attività

```json
{
    "name": "MyPipelineName",
    "properties": {
      "activities": [
        {
          "name": "MyCopyBlobtoSqlActivity",
          "type": "Copy",
          "typeProperties": {
            ...
          },
         "policy": {
            "timeout": "00:10:00",
            "retry": 1,
            "retryIntervalInSeconds": 60,
            "secureOutput": true
         }
        }
      ],
        "parameters": {
           ...
        }
    }
}
```

Nome JSON | Descrizione | Valori consentiti | Obbligatoria
--------- | ----------- | -------------- | --------
timeout | Specifica il timeout per l'attività da eseguire. | TimeSpan | No. Il timeout predefinito è 7 giorni.
retry | Numero massimo di tentativi | Integer | No. Il valore predefinito è 0
retryIntervalInSeconds | Il ritardo tra tentativi di ripetizione espresso in secondi | Integer | No. Il valore predefinito è 30 secondi
secureOutput | Se impostato su true, l'output dell'attività viene considerato protetto e non viene registrato per il monitoraggio. | Boolean | No. L'impostazione predefinita è false.

### <a name="control-activity"></a>Attività di controllo
Le attività di controllo presentano la seguente struttura di primo livello:

```json
{
    "name": "Control Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "dependsOn":
    {
    }
}
```

Tag | Descrizione | Obbligatoria
--- | ----------- | --------
name | Nome dell'attività. Specificare un nome che rappresenti l'azione eseguita dall'attività.<br/><ul><li>Numero massimo di caratteri: 55</li><li>Deve iniziare con una lettera, un numero o un carattere di sottolineatura (\_)</li><li>Non sono consentiti i caratteri seguenti: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \" | Sì</li><ul>
description | Testo descrittivo per il tipo o lo scopo dell'attività | Sì
type | Tipo di attività. Vedere le sezioni [attività di spostamento dei dati](#data-movement-activities), [attività di trasformazione dei dati](#data-transformation-activities)e [attività di controllo](#control-flow-activities) per diversi tipi di attività. | Sì
typeProperties | Le proprietà nella sezione typeProperties dipendono da ogni tipo di attività. Per visualizzare le proprietà del tipo per un'attività, fare clic sui collegamenti all'attività nella sezione precedente. | No
dependsOn | Questa proprietà viene usata per definire la dipendenza delle attività e come le attività successive dipendono dalle attività precedenti. Per altre informazioni, vedere [Dependency Activity](#activity-dependency). | No

### <a name="activity-dependency"></a>Dipendenza di attività
La dipendenza di attività definisce il modo in cui le attività successive dipendono dalle attività precedenti, determinando la condizione per la continuazione dell'esecuzione dell'attività successiva. Un'attività può dipendere da una o più attività precedenti con condizioni di dipendenza diverse.

Le diverse condizioni di dipendenza sono: Succeeded (esito positivo), Failed (esito negativo), Skipped (operazione ignorata), Completed (operazione completata).

Ad esempio, se una pipeline dispone di un'attività A-> attività B, i diversi scenari possibili sono:

- L'attività B ha una condizione di dipendenza dall'attività A con **succeeded**: l'attività B viene eseguita solo se lo stato finale dell'attività A è con esito positivo.
- L'attività B ha una condizione di dipendenza dall'attività A con **failed**: l'attività B viene eseguita solo se lo stato finale dell'attività A è con esito negativo.
- L'attività B ha una condizione di dipendenza dall'attività A con **completed**: l'attività B viene eseguita solo se lo stato finale dell'attività A è con esito positivo o negativo.
- L'attività B ha una condizione di dipendenza sull'attività A con **ignorato**: l'attività b viene eseguita se l'attività a ha uno stato finale ignorato. Lo stato skipped (operazione ignorata) si verifica nello scenario Attività X -> Attività Y -> Attività Z, in cui ogni attività viene eseguita solo se l'attività precedente ha esito positivo. Se l'attività X ha esito negativo, lo stato dell'attività Y è "ignorato" perché non viene mai eseguito. Analogamente, l'attività Z presenta anche lo stato "ignorato".

#### <a name="example-activity-2-depends-on-the-activity-1-succeeding"></a>Esempio: l'attività 2 dipende dall'esito positivo dell'attività 1

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities": [
         {
            "name": "MyFirstActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            }
        },
        {
            "name": "MySecondActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            },
            "dependsOn": [
            {
                "activity": "MyFirstActivity",
                "dependencyConditions": [
                    "Succeeded"
                ]
            }
          ]
        }
      ],
      "parameters": {
       }
    }
}

```

## <a name="sample-copy-pipeline"></a>Esempio di una pipeline di copia
In questa pipeline di esempio è presente un'attività di tipo **Copy** in the **attività** . In questo esempio, l' [attività di copia](copy-activity-overview.md) copia i dati da un archivio BLOB di Azure a un database nel database SQL di Azure.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "policy": {
          "retry": 2,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Tenere presente quanto segue:

- Nella sezione delle attività esiste una sola attività con l'oggetto **type** impostato su **Copy**.
- L'input per l'attività è impostato su **InputDataset** e l'output è impostato su **OutputDataset**. Vedere l'articolo [Set di dati](concepts-datasets-linked-services.md) per la definizione di set di dati in JSON.
- Nella sezione **typeProperties** vengono specificati **BlobSource** come tipo di origine e **SqlSink** come tipo di sink. Nella sezione [attività di spostamento dati](#data-movement-activities) fare clic sull'archivio dati che si vuole usare come origine o sink per ottenere altre informazioni sullo spostamento dei dati da e verso tale archivio dati.

Per la procedura dettagliata sulla creazione di questa pipeline, vedere [Avvio rapido: Creare una data factory](quickstart-create-data-factory-powershell.md).

## <a name="sample-transformation-pipeline"></a>Esempio di una pipeline di trasformazione
In questa pipeline di esempio è presente un'attività di tipo **HDInsightHive** in the **attività** . In questo esempio, l' [attività Hive di HDInsight](transform-data-using-hadoop-hive.md) trasforma i dati da un archivio BLOB di Azure tramite l'esecuzione di un file di script Hive in un cluster Hadoop di HDInsight.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ]
    }
}
```
Tenere presente quanto segue:

- Nella sezione attività esiste una sola attività con l'oggetto **type** impostato su **HDInsightHive**.
- Il file di script hive, **partitionweblogs. HQL**, è archiviato nell'account di archiviazione di Azure (specificato da scriptLinkedService, denominato AzureStorageLinkedService) e nella cartella script nel contenitore `adfgetstarted` .
- La sezione `defines` viene usata per specificare le impostazioni di runtime che vengono passate allo script Hive come valori di configurazione Hive, ad esempio `{hiveconf:inputtable}` e `${hiveconf:partitionedtable}`.

La sezione **typeProperties** è diversa per ogni attività di trasformazione. Per altre informazioni sulle proprietà del tipo supportate per un'attività di trasformazione, fare clic sull'attività di trasformazione nelle [attività di trasformazione dei dati](#data-transformation-activities).

Per la procedura dettagliata sulla creazione di questa pipeline, vedere [Esercitazione: Trasformare i dati usando Spark](tutorial-transform-data-spark-powershell.md).

## <a name="multiple-activities-in-a-pipeline"></a>Attività multiple in una pipeline
Le due pipeline di due esempio precedenti contengono una sola attività. È possibile avere più di un'attività in una pipeline. Se sono disponibili più attività in una pipeline e le attività successive non dipendono da quelle precedenti, le attività possono essere eseguite in parallelo.

È possibile concatenare due attività usando la [dipendenza delle attività](#activity-dependency), che definisce in che modo le attività successive dipendono dalle attività precedenti, determinando la condizione se si passerà all'esecuzione dell'attività successiva. Un'attività può dipendere da una o più attività precedenti con condizioni di dipendenza diverse.

## <a name="scheduling-pipelines"></a>Pianificazione delle pipeline
Le pipeline vengono pianificate da trigger. Esistono diversi tipi di trigger (trigger dell'utilità di pianificazione, che consente di attivare le pipeline in base a una pianificazione basata sul tempo reale, nonché il trigger manuale, che attiva le pipeline su richiesta). Per altre informazioni sui trigger, vedere l'articolo [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md).

Per fare in modo che il trigger attivi l'esecuzione di una pipeline, è necessario includere un riferimento di pipeline della pipeline specifica nella definizione del trigger. Pipeline e trigger hanno una relazione n-m. Più trigger possono avviare una singola pipeline e lo stesso trigger può avviare più pipeline. Dopo che il trigger è stato definito è necessario avviarlo per iniziare ad attivare la pipeline. Per altre informazioni sui trigger, vedere l'articolo [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md).

Si immagini, ad esempio, di avere un trigger dell'utilità di pianificazione, "trigger A", che desidero avviare la pipeline, ovvero "MyCopyPipeline". Definire il trigger, come illustrato nell'esempio seguente:

### <a name="trigger-a-definition"></a>Definizione del trigger A

```json
{
  "name": "TriggerA",
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      ...
      }
    },
    "pipeline": {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "MyCopyPipeline"
      },
      "parameters": {
        "copySourceName": "FileSource"
      }
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi
Vedere le esercitazioni seguenti per istruzioni dettagliate sulla creazione di pipeline con le attività:

- [Creare una pipeline con un'attività di copia](quickstart-create-data-factory-powershell.md)
- [Creare una pipeline con un'attività di trasformazione dati](tutorial-transform-data-spark-powershell.md)
