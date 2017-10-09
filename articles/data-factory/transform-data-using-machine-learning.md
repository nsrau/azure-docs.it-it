---
title: Creare pipeline di dati predittive tramite Azure Data Factory | Microsoft Docs
description: "Informazioni su come creare una pipeline predittiva mediante Azure Machine Learning - Attività di esecuzione batch in Azure Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: shengc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 413f12d301a0e2c47048d23b2d4fb7de6423256d
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Creare pipeline predittive tramite Azure Machine Learning e Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - GA](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Versione 2 - Anteprima](transform-data-using-machine-learning.md)

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) consente di compilare, testare e distribuire soluzioni di analisi predittiva. Da un punto di vista generale, questo avviene in tre passaggi:

1. **Creare un esperimento di training**. Questo passaggio deve essere eseguito con Azure ML Studio. Azure ML Studio è un ambiente di sviluppo visivo di collaborazione usato per eseguire il training e il test di un modello di analisi predittiva usando dati di training.
2. **Convertirlo in un esperimento predittivo**. Dopo aver eseguito il training del modello con i dati esistenti, preparare e semplificare l'esperimento di assegnazione dei punteggi quando si è pronti a usarlo per valutare nuovi dati.
3. **Distribuirlo come servizio Web**. È possibile pubblicare l'esperimento di assegnazione dei punteggi come servizio Web di Azure. È possibile inviare dati al modello tramite l'endpoint di questo servizio Web e ricevere le stime dei risultati dal modello.  

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale (GA), vedere [Attività di esecuzione batch di Machine Learning versione 1](v1/data-factory-azure-ml-batch-execution-activity.md).


### <a name="data-factory-and-machine-learning-together"></a>Data Factory e Machine Learning
Azure Data Factory consente di creare facilmente pipeline che usano un servizio Web pubblicato di [Azure Machine Learning][azure-machine-learning] per l'analisi predittiva. Con **Attività di esecuzione batch** in una pipeline di Data factory di Azure è possibile richiamare un servizio Web di Azure ML per eseguire stime dei dati in batch. 

Nel corso del tempo è necessario ripetere il training dei modelli predittivi negli esperimenti di assegnazione dei punteggi di Azure ML usando nuovi set di dati di input. È possibile ripetere il training di un modello di Azure ML da una pipeline di Data factory seguendo questa procedura:

1. Pubblicare l'esperimento di training, non l'esperimento predittivo, come servizio Web. Eseguire questo passaggio in Azure ML Studio come è stato fatto per esporre l'esperimento predittivo come servizio Web nello scenario precedente.
2. Usare Attività di esecuzione batch di Azure ML per richiamare il servizio Web per l'esperimento di training. In sostanza, è possibile usare Attività di esecuzione batch di Azure ML per richiamare sia il servizio Web di training che il servizio Web di assegnazione dei punteggi.

Al termine della ripetizione del training, aggiornare il servizio Web di assegnazione dei punteggi, ovvero l'esperimento predittivo esposto come servizio Web, con il modello appena sottoposto a training usando l'**Attività della risorsa di aggiornamento di Azure ML**. Per informazioni dettagliate, vedere l'articolo [Updating models using Update Resource Activity](update-machine-learning-models.md) (Aggiornamento dei modelli con Attività della risorsa di aggiornamento).

## <a name="azure-machine-learning-linked-service"></a>Servizio collegato di Azure Machine Learning

Si crea un servizio collegato di **Azure Machine Learning** per collegare un servizio web di Azure Machine Learning a una data factory di Azure. Il servizio collegato viene usato da Attività di esecuzione batch di Azure Machine Learning e da [Attività della risorsa di aggiornamento](update-machine-learning-models.md). 


```JSON
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md) per le descrizioni sulle proprietà nella definizione JSON. 

Azure Machine Learning supporta sia i servizi Web classici che i nuovi servizi Web per l'esperimento predittivo. È possibile scegliere quello corretto da usare da Data Factory. Per ottenere le informazioni necessarie per creare il servizio collegato di Azure Machine Learning, andare a https://services.azureml.net, dove sono elencati tutti i servizi Web nuovi e i servizi Web classici. Fare clic sul servizio Web cui si desidera accedere e fare clic sulla pagina **Consume (Uso)**. Copiare la **chiave primaria** per la proprietà **apiKey** e le **richieste batch** per la proprietà **mlEndpoint**. 

![Servizi Web di Azure Machine Learning](./media/transform-data-using-machine-learning/web-services.png)

##<a name="azure-machine-learning-batch-execution-activity"></a>Attività di esecuzione batch di Azure Machine Learning

Il frammento JSON seguente definisce un'attività di esecuzione batch di Azure Machine Learning. La definizione dell'attività contiene un riferimento al servizio collegato di Azure Machine Learning creato in precedenza. 

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                }, 
                "FilePath":"path1"
            }, 
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference" 
                }, 
                "FilePath":"path2"
            }        
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"path3"
            }, 
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"path4"
            }         
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```



| Proprietà          | Descrizione                              | Obbligatorio |
| :---------------- | :--------------------------------------- | :------- |
| name              | Nome dell'attività nella pipeline     | Sì      |
| Descrizione       | Testo che descrive l'attività.  | No       |
| type              | Per l'attività U-SQL di Data Lake Analytics, il tipo di attività è **AzureMLBatchExecution**. | Sì      |
| linkedServiceName | Servizi collegati al servizio collegato di Azure Machine Learning. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md). | Sì      |
| webServiceInputs  | Coppie valore, chiave, che eseguono il mapping dei nomi di input dei servizi Web di Azure Machine Learning. La chiave deve corrispondere ai parametri di input definiti nel servizio Web pubblicato di Azure Machine Learning. Il valore è una coppia di proprietà FilePath e dei servizi collegati di archiviazione di Azure che specifica i percorsi BLOB di input. | No       |
| webServiceOutputs | Coppie valore, chiave, che eseguono il mapping dei nomi di output dei servizi Web di Azure Machine Learning. La chiave deve corrispondere ai parametri di output definiti nel servizio Web pubblicato di Azure Machine Learning. Il valore è una coppia di proprietà FilePath e dei servizi collegati di archiviazione di Azure che specifica i percorsi BLOB di output. | No       |
| globalParameters  | Coppie di valore, chiave da passare all'endpoint del servizio esecuzione batch di Azure Machine Learning. Le chiavi devono corrispondere ai nomi dei parametri dei servizi Web definiti nel servizio Web pubblicato di Azure Machine Learning. I valori vengono passati nella proprietà GlobalParameters della richiesta di esecuzione batch di Azure Machine Learning | No       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenario 1: Esperimenti di uso degli input/output del servizio Web che fanno riferimento ai dati presenti nell'archiviazione BLOB di Azure

Il questo scenario il servizio Web Azure Machine Learning esegue stime usando dati provenienti da un file dell'archiviazione BLOB di Azure e archivia i risultati nell'archiviazione BLOB. Il frammento di codice JSON seguente definisce una pipeline di Data factory con un'attività AzureMLBatchExecution. Viene fatto riferimento ai dati di input e output nell'archiviazione BLOB di Azure tramite una coppia di LinkedName e FilePath. Nell'esempio i servizi collegati di input e output sono diversi; è possibile usare servizi collegati diversi per ognuno degli input/output per Data Factory per poter selezionare i file corretti e inviarli al servizio Web di Azure Machine Learning. 

> [!IMPORTANT]
> Nell'esperimento di Machine Learning di Azure,le porte e i parametri globali di input e output del servizio Web hanno nomi predefiniti ("input1", "input2") che è possibile personalizzare. I nomi scelti per le impostazioni webServiceInputs, webServiceOutputs e globalParameters devono corrispondere esattamente ai nomi negli esperimenti. Per verificare il mapping previsto, è possibile visualizzare il payload della richiesta di esempio nella pagina della Guida relativa all'esecuzione in batch per l'endpoint di Machine Learning di Azure.
>
> 

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                }, 
                "FilePath":"amltest/input/in1.csv"
            }, 
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference" 
                }, 
                "FilePath":"amltest/input/in2.csv"
            }        
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"amltest2/output/out1.csv"
            }, 
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"amltest2/output/out2.csv"
            }         
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenario 2: Esperimenti di uso dei moduli Reader e Writer per fare riferimento ai dati in diversi archivi
Un altro scenario comune durante la creazione di esperimenti di Azure Machine Learning è quello relativo all'uso dei moduli Import Data e Output Data. Il modulo Import Data consente di caricare i dati in un esperimento, mentre il modulo Output Data consente di salvare i dati derivanti dagli esperimenti. Per informazioni dettagliate sui moduli Import Data e Output Data, vedere gli argomenti [Import Data](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Output Data](https://msdn.microsoft.com/library/azure/dn905984.aspx) in MSDN Library.     

Quando si usano i moduli Import Data e Output Data, è consigliabile usare un parametro del servizio Web per ogni proprietà di tali moduli. Questi parametri Web consentono di configurare i valori durante il runtime. Ad esempio, è possibile creare un esperimento con un modulo Import Data che usa un database SQL di Azure: XXX.database.windows.net. Dopo aver distribuito il servizio Web, si intende consentire ai consumer del servizio Web di specificare un altro server SQL di Azure denominato `YYY.database.windows.net`. È possibile usare un parametro del servizio Web per consentire la configurazione di questo valore.

> [!NOTE]
> L'input e l'output del servizio Web sono diversi dai parametri del servizio Web. Nel primo scenario è stato illustrato come è possibile specificare un input e un output per un servizio Web Azure ML. In questo scenario si passano i parametri per un servizio Web corrispondenti alle proprietà dei moduli Import Data e Output Data.
>
> 

Viene preso in esame uno scenario relativo all'uso dei parametri del servizio Web. È stato distribuito un servizio Web di Azure Machine Learning che usa un modulo Reader per leggere i dati da una delle origini dati di Azure Machine Learning supportate, ad esempio un database SQL di Azure. Dopo l'esecuzione batch, i risultati vengono scritti usando un modulo Writer (database SQL di Azure).  Negli esperimenti non sono definiti input e output del servizio Web. In questo caso, è consigliabile configurare i parametri del servizio Web rilevanti per i moduli Reader e Writer. Ciò consente la configurazione dei moduli Reader e Writer quando si usa l'attività AzureMLBatchExecution. Specificare i parametri del servizio Web nella sezione **globalParameters** del codice JSON dell'attività come indicato di seguito.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```


> [!NOTE]
> I parametri del servizio Web applicano la distinzione tra maiuscole e minuscole. È quindi necessario assicurarsi che i nomi specificati nel file JSON dell'attività corrispondano ai nomi esposti dal servizio Web.
>

Al termine della ripetizione del training, aggiornare il servizio Web di assegnazione dei punteggi, ovvero l'esperimento predittivo esposto come servizio Web, con il modello appena sottoposto a training usando l'**Attività della risorsa di aggiornamento di Azure ML**. Per informazioni dettagliate, vedere l'articolo [Updating models using Update Resource Activity](update-machine-learning-models.md) (Aggiornamento dei modelli con Attività della risorsa di aggiornamento).



## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti che illustrano come trasformare i dati in altri modi: 

* [Attività U-SQL](transform-data-using-data-lake-analytics.md)
* [Attività Hive](transform-data-using-hadoop-hive.md)
* [Attività Pig](transform-data-using-hadoop-pig.md)
* [Attività MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Attività di streaming di Hadoop](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)
* [Attività stored procedure](transform-data-using-stored-procedure.md)

