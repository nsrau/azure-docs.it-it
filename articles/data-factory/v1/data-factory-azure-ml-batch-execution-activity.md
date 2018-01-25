---
title: Creare pipeline di dati predittive tramite Azure Data Factory | Microsoft Docs
description: Illustra come creare pipeline predittive usando Data factory di Azure e Azure Machine Learning
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 4fad8445-4e96-4ce0-aa23-9b88e5ec1965
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 05ae7cdc78e909c9aaa2b690d03eff8da09b6242
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Creare pipeline predittive tramite Azure Machine Learning e Azure Data Factory

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Attività Hive](data-factory-hive-activity.md) 
> * [Attività di Pig](data-factory-pig-activity.md)
> * [Attività MapReduce](data-factory-map-reduce.md)
> * [Attività di Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Attività Spark](data-factory-spark.md)
> * [Attività di esecuzione batch di Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Attività della risorsa di aggiornamento di Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Attività stored procedure](data-factory-stored-proc-activity.md)
> * [Attività U-SQL di Data Lake Analytics](data-factory-usql-activity.md)
> * [Attività personalizzata di .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introduzione
> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere le informazioni su come [trasformare dati tramite Machine Learning in Data Factory versione 2](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) consente di compilare, testare e distribuire soluzioni di analisi predittiva. Da un punto di vista generale, questo avviene in tre passaggi:

1. **Creare un esperimento di training**. Questo passaggio deve essere eseguito con Azure ML Studio. Azure ML Studio è un ambiente di sviluppo visivo di collaborazione usato per eseguire il training e il test di un modello di analisi predittiva usando dati di training.
2. **Convertirlo in un esperimento predittivo**. Dopo aver eseguito il training del modello con i dati esistenti, preparare e semplificare l'esperimento di assegnazione dei punteggi quando si è pronti a usarlo per valutare nuovi dati.
3. **Distribuirlo come servizio Web**. È possibile pubblicare l'esperimento di assegnazione dei punteggi come servizio Web di Azure. È possibile inviare dati al modello tramite l'endpoint di questo servizio Web e ricevere le stime dei risultati dal modello.  

### <a name="azure-data-factory"></a>Data factory di Azure
Data factory è un servizio di integrazione dei dati basato sul cloud che permette di automatizzare lo **spostamento** e la **trasformazione** dei dati. È possibile creare soluzioni di integrazione dei dati usando Azure Data Factory che può inserire dati da diversi archivi dati, trasformare/elaborare i dati e pubblicare i dati risultanti negli archivi dati.

Il servizio Data Factory consente di creare pipeline di dati che spostano e trasformano i dati e quindi di eseguire le pipeline in base a una pianificazione specificata (ogni ora, ogni giorno, ogni settimana e così via). Offre anche viste avanzate per visualizzare la derivazione e le dipendenze tra le pipeline di dati e monitorare tutte le pipeline di dati da una singola visualizzazione unificata per individuare facilmente i problemi e configurare avvisi di monitoraggio.

Per una rapida introduzione al servizio Azure Data Factory, vedere gli articoli [Introduzione al servizio Azure Data Factory](data-factory-introduction.md) e [Creare la prima pipeline](data-factory-build-your-first-pipeline.md).

### <a name="data-factory-and-machine-learning-together"></a>Data Factory e Machine Learning
Azure Data Factory consente di creare facilmente pipeline che usano un servizio Web pubblicato di [Azure Machine Learning][azure-machine-learning] per l'analisi predittiva. Con **Attività di esecuzione batch** in una pipeline di Data factory di Azure è possibile richiamare un servizio Web di Azure ML per eseguire stime dei dati in batch. Per altre informazioni, vedere la sezione [Richiamo di un servizio Web di Azure ML tramite Attività di esecuzione batch](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity) .

Nel corso del tempo è necessario ripetere il training dei modelli predittivi negli esperimenti di assegnazione dei punteggi di Azure ML usando nuovi set di dati di input. È possibile ripetere il training di un modello di Azure ML da una pipeline di Data factory seguendo questa procedura:

1. Pubblicare l'esperimento di training, non l'esperimento predittivo, come servizio Web. Eseguire questo passaggio in Azure ML Studio come è stato fatto per esporre l'esperimento predittivo come servizio Web nello scenario precedente.
2. Usare Attività di esecuzione batch di Azure ML per richiamare il servizio Web per l'esperimento di training. In sostanza, è possibile usare Attività di esecuzione batch di Azure ML per richiamare sia il servizio Web di training che il servizio Web di assegnazione dei punteggi.

Al termine della ripetizione del training, aggiornare il servizio Web di assegnazione dei punteggi, ovvero l'esperimento predittivo esposto come servizio Web, con il modello appena sottoposto a training usando l'**Attività della risorsa di aggiornamento di Azure ML**. Per informazioni dettagliate, vedere l'articolo [Updating models using Update Resource Activity](data-factory-azure-ml-update-resource-activity.md) (Aggiornamento dei modelli con Attività della risorsa di aggiornamento).

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Richiamo di un servizio Web tramite Attività di esecuzione batch
È possibile usare Data factory di Azure per gestire l'elaborazione e lo spostamento dei dati e quindi effettuare un'esecuzione batch tramite Azure Machine Learning. Ecco i passaggi principali:

1. Creare un servizio collegato di Azure Machine Learning. Sono necessari i valori seguenti:

   1. **URI della richiesta** per l’API di esecuzione batch. È possibile trovare l'URI della richiesta facendo clic sul collegamento **ESECUZIONE BATCH** nella pagina dei servizi Web.
   2. **API key** per il servizio Web di Azure Machine Learning pubblicato. È possibile trovare la chiave API facendo clic sul servizio Web pubblicato.
   3. Usare l'attività **AzureMLBatchExecution** .

      ![Dashboard di Machine Learning](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Batch URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenario: Esperimenti di uso degli input/output del servizio Web che fanno riferimento ai dati presenti nell'archiviazione BLOB di Azure
Il questo scenario il servizio Web Azure Machine Learning esegue stime usando dati provenienti da un file dell'archiviazione BLOB di Azure e archivia i risultati nell'archiviazione BLOB. Il frammento di codice JSON seguente definisce una pipeline di Data factory con un'attività AzureMLBatchExecution. L'attività include il set di dati **DecisionTreeInputBlob** come input e il set di dati **DecisionTreeResultBlob** come output. Il set di dati **DecisionTreeInputBlob** viene passato come input al servizio Web usando la proprietà JSON **webServiceInput**. Il set di dati **DecisionTreeResultBlob** viene passato come output al servizio Web usando la proprietà JSON **webServiceOutputs**.  

> [!IMPORTANT]
> Se il servizio Web accetta più input, usare la proprietà **webServiceInputs** invece di **webServiceInput**. Vedere la sezione [Il servizio Web richiede più input](#web-service-requires-multiple-inputs) per un esempio di uso della proprietà webServiceInputs.
>
> I set di dati a cui fanno riferimento le proprietà **webServiceInput**/**webServiceInputs** e **webServiceOutputs** (in **typeProperties**) devono essere inclusi anche negli **input** e **output** dell'attività.
>
> Nell'esperimento di Machine Learning di Azure,le porte e i parametri globali di input e output del servizio Web hanno nomi predefiniti ("input1", "input2") che è possibile personalizzare. I nomi scelti per le impostazioni webServiceInputs, webServiceOutputs e globalParameters devono corrispondere esattamente ai nomi negli esperimenti. Per verificare il mapping previsto, è possibile visualizzare il payload della richiesta di esempio nella pagina della Guida relativa all'esecuzione in batch per l'endpoint di Machine Learning di Azure.
>
>

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }                
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```
> [!NOTE]
> Possono essere passati come parametri per il servizio Web solo input e output dell'attività AzureMLBatchExecution. Nel precedente snippet JSON, ad esempio, DecisionTreeInputBlob è un input per l'attività AzureMLBatchExecution e viene passato come input al servizio Web tramite il parametro webServiceInput.   
>
>

### <a name="example"></a>Esempio
Questo esempio usa Archiviazione di Azure per archiviare i dati di input e di output.

Prima di procedere con questo esempio, è consigliabile eseguire l'esercitazione relativa alla [creazione della prima pipeline con Data Factory][adf-build-1st-pipeline]. Usare l'editor di Data Factory per creare elementi di Data Factory, come servizi collegati, set di dati e pipeline, in questo esempio.   

1. Creare un **servizio collegato** per **Archiviazione di Azure**. Se i file di input e output si trovano in account di archiviazione diversi, sono necessari due servizi collegati. Di seguito è fornito un esempio JSON:

    ```JSON
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
        }
      }
    }
    ```
2. Creare il **set di dati** di **input** di Azure Data Factory. A differenza di altri set di dati di Data Factory, questi devono includere entrambi i valori **folderPath** e **fileName**. È possibile usare il partizionamento per fare in modo che ogni esecuzione di batch (ogni sezione di dati) elabori o produca file di input e di output univoci. Può essere necessario includere alcune attività upstream per trasformare il file di input in formato CSV e inserirlo nell'account di archiviazione per ogni sezione. In tal caso, non è necessario includere le impostazioni **external** ed **externalData** illustrate nell'esempio seguente e DecisionTreeInputBlob sarà il set di dati di output di un'attività diversa.

    ```JSON
    {
      "name": "DecisionTreeInputBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/input",
          "fileName": "in.csv",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }
    ```

    Il file con estensione csv di input deve avere una riga di intestazione di colonna. Se si usa l'**attività di copia** per creare/spostare il file CSV nell'archivio BLOB, è consigliabile impostare la proprietà **blobWriterAddHeader** del sink su **true**. Ad esempio: 

    ```JSON
    sink:
    {
        "type": "BlobSink",     
        "blobWriterAddHeader": true
    }
    ```

    Se il file CSV non ha la riga di intestazione, potrebbe essere visualizzato un messaggio di errore analogo al seguente: **Errore nell'attività: Errore di lettura della stringa. Token imprevisto: StartObject. Percorso '', riga 1, posizione 1**.
3. Creare il **set di dati** di **output** di Azure Data Factory. In questo esempio il file di output usa il partizionamento per creare un percorso di output univoco per l'esecuzione di ciascuna sezione. Senza il partizionamento l'attività sovrascriverà il file.

    ```JSON
    {
      "name": "DecisionTreeResultBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/scored/{folderpart}/",
          "fileName": "{filepart}result.csv",
          "partitionedBy": [
            {
              "name": "folderpart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyyMMdd"
              }
            },
            {
              "name": "filepart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HHmmss"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 15
        }
      }
    }
    ```
4. Creare un **servizio collegato** di tipo **AzureMLLinkedService** che fornisce la chiave API e un modello di URL per l'esecuzione batch.

    ```JSON
    {
      "name": "MyAzureMLLinkedService",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
          "mlEndpoint": "https://[batch execution endpoint]/jobs",
          "apiKey": "[apikey]"
        }
      }
    }
    ```
5. Creare infine una pipeline contenente un'attività **AzureMLBatchExecution** . In fase di esecuzione la pipeline segue questa procedura:

   1. Ottiene il percorso del file di input dal set di dati di input.
   2. Richiama l'API di esecuzione batch di Azure Machine Learning.
   3. Copia l'output di esecuzione batch nel BLOB specificato nel set di dati di output.

      > [!NOTE]
      > L'attività AzureMLBatchExecution può avere zero o più input e uno o più output.
      >
      >

    ```JSON
    {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [
            {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                {
                    "name": "DecisionTreeInputBlob"
                }
                ],
                "outputs": [
                {
                    "name": "DecisionTreeResultBlob"
                }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }                
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
    }
    ```

      Per la data e l'ora di **inizio** e di **fine** è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). ad esempio 2014-10-14T16:32:41Z. Se non si specifica un valore per la proprietà **Inizio + 48 ore** ". Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "**start + 48 hours**". Per eseguire la pipeline illimitatamente, specificare **9999-09-09** come valore per la proprietà **end**. Per informazioni dettagliate sulle proprietà JSON, vedere [Informazioni di riferimento sugli script JSON di Data Factory](https://msdn.microsoft.com/library/dn835050.aspx) .

      > [!NOTE]
      > La specifica dell'input per l'attività AzureMLBatchExecution è opzionale.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenario: Esperimenti di uso dei moduli Reader e Writer per fare riferimento ai dati in diversi archivi
Un altro scenario comune durante la creazione di esperimenti di Azure ML è quello relativo all'uso dei moduli Reader e Writer. Il modulo Reader consente di caricare i dati in un esperimento, mentre il modulo Writer consente di salvare i dati derivanti dagli esperimenti. Per informazioni dettagliate sui moduli Reader e Writer, vedere gli argomenti [Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) in MSDN Library.     

Quando si usano i moduli Reader e Writer, è consigliabile usare un parametro del servizio Web per ciascuna proprietà di tali moduli. Questi parametri Web consentono di configurare i valori durante il runtime. Ad esempio, è possibile creare un esperimento con un modulo Reader che usa un database SQL di Azure: XXX.database.windows.net. Dopo aver distribuito il servizio Web, si desidera consentire ai consumer del servizio Web di specificare un altro server SQL di Azure denominato YYY.database.windows.net. È possibile usare un parametro del servizio Web per consentire la configurazione di questo valore.

> [!NOTE]
> L'input e l'output del servizio Web sono diversi dai parametri del servizio Web. Nel primo scenario è stato illustrato come è possibile specificare un input e un output per un servizio Web Azure ML. In questo scenario si passano i parametri per un servizio Web corrispondenti alle proprietà dei moduli Reader e Writer.
>
>

Viene preso in esame uno scenario relativo all'uso dei parametri del servizio Web. È stato distribuito un servizio Web di Azure Machine Learning che usa un modulo Reader per leggere i dati da una delle origini dati di Azure Machine Learning supportate, ad esempio un database SQL di Azure. Dopo l'esecuzione batch, i risultati vengono scritti usando un modulo Writer (database SQL di Azure).  Negli esperimenti non sono definiti input e output del servizio Web. In questo caso, è consigliabile configurare i parametri del servizio Web rilevanti per i moduli Reader e Writer. Ciò consente la configurazione dei moduli Reader e Writer quando si usa l'attività AzureMLBatchExecution. Specificare i parametri del servizio Web nella sezione **globalParameters** del codice JSON dell'attività come indicato di seguito.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

È anche possibile usare le [funzioni di Data factory](data-factory-functions-variables.md) per passare i valori per i parametri del servizio Web, come illustrato nell'esempio seguente:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> I parametri del servizio Web applicano la distinzione tra maiuscole e minuscole. È quindi necessario assicurarsi che i nomi specificati nel file JSON dell'attività corrispondano ai nomi esposti dal servizio Web.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Uso di un modulo Reader per leggere dati da più file del BLOB di Azure
Le pipeline di Big Data, con attività come Pig e Hive, possono generare uno o più file di output senza estensioni. Ad esempio, quando si specifica una tabella Hive esterna, i dati per tale tabella possono essere archiviati nell'archiviazione BLOB di Azure con il nome 000000_0. È possibile usare il modulo Reader in un esperimento per la lettura di più file e usare questi ultimi per creare delle stime.

Quando si usa il modulo Reader in un esperimento di Azure Machine Learning, è possibile specificare il BLOB di Azure come input. I file nell'archivio BLOB di Azure possono essere file di output, ad esempio 000000_0, generati da uno script Pig e Hive in esecuzione in HDInsight. Il modulo Reader consente di leggere i file, senza estensioni, configurando la voce **Path to container, directory or blob**(Percorso del contenitore, della directory o del BLOB). La parte relativa al **percorso del contenitore** punta al contenitore, mentre **directory o BLOB** punta alla cartella che contiene i file, come illustrato nell'immagine seguente. L'asterisco (\*) **specifica che tutti i file nel contenitore o nella cartella, ovvero data/aggregateddata/year=2014/month-6/\***, vengono letti come parte dell'esperimento.

![Proprietà del Blob Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Esempio
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Pipeline con l'attività AzureMLBatchExecution con parametri del servizio Web

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure ML model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }              
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

Nell'esempio JSON precedente:

* Il servizio Web Azure Machine Learning distribuito usa un modulo Reader e un modulo Writer per leggere e scrivere i dati da e in un database SQL di Azure. Il servizio Web espone i quattro parametri seguenti: Database server name, Database name, Server user account name e Server user account password.  
* Per la data e l'ora di **inizio** e di **fine** è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). ad esempio 2014-10-14T16:32:41Z. Se non si specifica un valore per la proprietà **Inizio + 48 ore** ". Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "**start + 48 hours**". Per eseguire la pipeline illimitatamente, specificare **9999-09-09** come valore per la proprietà **end**. Per informazioni dettagliate sulle proprietà JSON, vedere [Informazioni di riferimento sugli script JSON di Data Factory](https://msdn.microsoft.com/library/dn835050.aspx) .

### <a name="other-scenarios"></a>Altri scenari
#### <a name="web-service-requires-multiple-inputs"></a>Il servizio Web richiede più input
Se il servizio Web accetta più input, usare la proprietà **webServiceInputs** invece di **webServiceInput**. Includere anche i set di dati a cui **webServiceInputs** fa riferimento negli **input** dell'attività.

Nell'esperimento di Machine Learning di Azure,le porte e i parametri globali di input e output del servizio Web hanno nomi predefiniti ("input1", "input2") che è possibile personalizzare. I nomi scelti per le impostazioni webServiceInputs, webServiceOutputs e globalParameters devono corrispondere esattamente ai nomi negli esperimenti. Per verificare il mapping previsto, è possibile visualizzare il payload della richiesta di esempio nella pagina della Guida relativa all'esecuzione in batch per l'endpoint di Machine Learning di Azure.

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Servizio Web non richiede un input
I servizi Web per l'esecuzione batch di Azure ML possono essere usati per eseguire flussi di lavoro, ad esempio script R o Python, che possono non richiedere alcun input. In alternativa, l'esperimento può essere configurato con un modulo Reader che non espone proprietà GlobalParameters. In tal caso l'attività AzureMLBatchExecution verrà configurata come segue:

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }              
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Servizio Web non richiede un input/output
Il servizio Web per l'esecuzione batch di Azure ML può non disporre di alcun output del servizio Web configurato. In questo esempio non sono disponibili input o output del servizio Web, né alcuna proprietà GlobalParameters configurata. È ancora presente un output configurato nell'attività stessa, ma non viene fornito come webServiceOutput.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Il servizio Web usa moduli Reader e Writer e l'attività viene eseguita solo quando le altre attività hanno avuto esito positivo
I moduli Reader e Writer del servizio Web di Azure ML possono essere configurati per l'esecuzione con o senza proprietà GlobalParameters. Tuttavia, è possibile incorporare chiamate al servizio in una pipeline che usa le dipendenze del set di dati per richiamare il servizio solo dopo il completamento di alcune operazioni di elaborazione upstream. Si possono anche attivare altre azioni dopo il completamento dell'esecuzione batch usando questo approccio. In tal caso è possibile esprimere le dipendenze tramite gli input e gli output dell'attività, senza denominarli input o output del servizio Web.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

Ecco i **punti chiave** :

* Se l'endpoint dell'esperimento usa la proprietà webServiceInput, questa è rappresentata da un set di dati del BLOB ed è inclusa negli input dell'attività e nella proprietà webServiceInput. In caso contrario, la proprietà webServiceInput viene omessa.
* Se l'endpoint dell'esperimento usa le proprietà webServiceOutputs, queste sono rappresentate da un set di dati del BLOB e sono incluse negli output dell'attività e nella proprietà webServiceOutputs. Il mapping degli output dell'attività e di webServiceOutputs viene eseguito in base al nome di ogni output nell'esperimento. In caso contrario, la proprietà webServiceOutputs viene omessa.
* Se l'endpoint dell'esperimento espone le proprietà globalParameters, vengono assegnate alla proprietà globalParameters dell'attività come coppie chiave-valore. In caso contrario, la proprietà globalParameters viene omessa. Le chiavi distinguono tra maiuscole e minuscole. [funzioni di Data factory di Azure](data-factory-functions-variables.md) possono essere usate nei valori.
* Nelle proprietà di input e output delle dell'attività possono essere inclusi set di dati aggiuntivi, senza che vi si faccia riferimento nella sezione typeProperties dell'attività. Questi set di dati regolano l'esecuzione tramite le dipendenze delle sezioni, ma in caso contrario vengono ignorati dall'attività AzureMLBatchExecution.


## <a name="updating-models-using-update-resource-activity"></a>Aggiornamento dei modelli con Attività della risorsa di aggiornamento
Al termine della ripetizione del training, aggiornare il servizio Web di assegnazione dei punteggi, ovvero l'esperimento predittivo esposto come servizio Web, con il modello appena sottoposto a training usando l'**Attività della risorsa di aggiornamento di Azure ML**. Per informazioni dettagliate, vedere l'articolo [Updating models using Update Resource Activity](data-factory-azure-ml-update-resource-activity.md) (Aggiornamento dei modelli con Attività della risorsa di aggiornamento).

### <a name="reader-and-writer-modules"></a>Moduli Reader e Writer 
Uno scenario comune per l'uso dei parametri del servizio Web è costituito dai reader e dai writer SQL di Azure. Il modulo Reader viene usato per caricare i dati in un esperimento dai servizi di gestione dati all'esterno di Azure Machine Learning Studio. Il modulo Writer viene usato per salvare i dati degli esperimenti in servizi di gestione dati all'esterno di Azure Machine Learning Studio.  

Per informazioni dettagliate sui BLOB di Azure o sui moduli Reader e Writer SQL di Azure, vedere gli argomenti [Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) in MSDN Library. L'esempio della sezione precedente usa un reader e un writer di BLOB di Azure. Questa sezione illustra l'uso di un reader e un writer SQL di Azure.

## <a name="frequently-asked-questions"></a>Domande frequenti
**D:** Ho più file generati dalle pipeline di Big Data. Posso usare l'attività AzureMLBatchExecution per lavorare con tali file?

**R:** Sì. Per informazioni dettagliate, vedere la sezione **Uso di un modulo Reader per leggere dati da più file del BLOB di Azure** .

## <a name="azure-ml-batch-scoring-activity"></a>Attività di assegnazione dei punteggi di batch di Azure ML
Se si sta usando l'attività **AzureMLBatchScoring** per l'integrazione con Azure Machine Learning, si consiglia di passare alla più recente attività **AzureMLBatchExecution**.

L'attività AzureMLBatchExecution è stata introdotta nella versione di Azure SDK e Azure PowerShell di agosto 2015.

Se si vuole continuare a usare l'attività AzureMLBatchScoring, continuare a leggere questa sezione.  

### <a name="azure-ml-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Attività di assegnazione dei punteggi di batch di Azure ML che usa Archiviazione di Azure per l'input/output

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Parametri del servizio Web
Per specificare i valori per i parametri del servizio Web, aggiungere una sezione **typeProperties** alla sezione **AzureMLBatchScoringActivity** nel file JSON della pipeline, come illustrato nell'esempio seguente:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
È anche possibile usare le [funzioni di Data factory](data-factory-functions-variables.md) per passare i valori per i parametri del servizio Web, come illustrato nell'esempio seguente:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> I parametri del servizio Web applicano la distinzione tra maiuscole e minuscole. È quindi necessario assicurarsi che i nomi specificati nel file JSON dell'attività corrispondano ai nomi esposti dal servizio Web.
>
>

## <a name="see-also"></a>Vedere anche
* [Post di blog di Azure: Guida introduttiva a Data Factory di Azure e ad Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
