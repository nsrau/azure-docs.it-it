---
title: Creare pipeline di dati predittivi
description: Informazioni su come creare una pipeline predittiva usando Azure Machine Learning Studio (versione classica)-attività di esecuzione batch in Azure Data Factory.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/16/2020
ms.openlocfilehash: dabb7b8cd8023fe88a8c8d6dc507a09623bd11dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86537681"
---
# <a name="create-a-predictive-pipeline-using-azure-machine-learning-studio-classic-and-azure-data-factory"></a>Creare una pipeline predittiva usando Azure Machine Learning Studio (classico) e Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Versione corrente](transform-data-using-machine-learning.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Machine Learning Studio (classico)](https://azure.microsoft.com/documentation/services/machine-learning/) consente di compilare, testare e distribuire soluzioni di analisi predittiva. Da un punto di vista generale, questo avviene in tre passaggi:

1. **Creare un esperimento di training**. Per eseguire questa operazione, usare il Azure Machine Learning Studio (versione classica). Azure Machine Learning Studio (classico) è un ambiente di sviluppo visivo collaborativo che consente di eseguire il training e il test di un modello di analisi predittiva usando i dati di training.
2. **Convertirlo in un esperimento predittivo**. Dopo aver eseguito il training del modello con i dati esistenti, preparare e semplificare l'esperimento di assegnazione dei punteggi quando si è pronti a usarlo per valutare nuovi dati.
3. **Distribuirlo come servizio Web**. È possibile pubblicare l'esperimento di assegnazione dei punteggi come servizio Web di Azure. È possibile inviare dati al modello tramite l'endpoint di questo servizio Web e ricevere le stime dei risultati dal modello.

### <a name="data-factory-and-azure-machine-learning-studio-classic-together"></a>Data Factory e Azure Machine Learning Studio (classico) insieme
Azure Data Factory consente di creare facilmente pipeline che usano un servizio Web pubblicato [Azure Machine Learning Studio (classico)](https://azure.microsoft.com/documentation/services/machine-learning) per l'analisi predittiva. Utilizzando l' **attività di esecuzione batch** in una pipeline di Azure Data Factory, è possibile richiamare un servizio web di Azure Machine Learning Studio (classico) per eseguire stime sui dati in batch.

Nel corso del tempo, è necessario ripetere il training dei modelli predittivi negli esperimenti di assegnazione dei punteggi Azure Machine Learning Studio (classico) usando nuovi set di dati di input. È possibile ripetere il training di un modello da una pipeline di Data Factory seguendo questa procedura:

1. Pubblicare l'esperimento di training, non l'esperimento predittivo, come servizio Web. Questa operazione viene eseguita nel Azure Machine Learning Studio (classico), come è stato fatto per esporre l'esperimento predittivo come servizio Web nello scenario precedente.
2. Usare l'attività di esecuzione batch Azure Machine Learning Studio (classica) per richiamare il servizio Web per l'esperimento di training. In pratica, è possibile usare l'attività di esecuzione batch Azure Machine Learning Studio (classica) per richiamare il servizio Web di training e il servizio Web di assegnazione dei punteggi.

Al termine della ripetizione del training, aggiornare il servizio Web di assegnazione dei punteggi (esperimento predittivo esposto come servizio Web) con il modello appena sottoposto a training usando l' **attività di aggiornamento della risorsa di Azure Machine Learning Studio (classica)**. Per informazioni dettagliate, vedere l'articolo [Updating models using Update Resource Activity](update-machine-learning-models.md) (Aggiornamento dei modelli con Attività della risorsa di aggiornamento).

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Servizio collegato Azure Machine Learning Studio (classico)

Si crea un servizio collegato **Azure Machine Learning Studio (classico)** per collegare un servizio Web di Azure Machine Learning Studio (classico) a una data factory di Azure. Il servizio collegato viene usato dall'attività di esecuzione batch Azure Machine Learning Studio (classica) e dall' [attività Aggiorna risorsa](update-machine-learning-models.md).

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md) per le descrizioni sulle proprietà nella definizione JSON.

Azure Machine Learning Studio (classico) supporta i servizi Web classici e i nuovi servizi Web per l'esperimento predittivo. È possibile scegliere quello corretto da usare da Data Factory. Per ottenere le informazioni necessarie per creare il servizio collegato Azure Machine Learning Studio (classico), vedere https://services.azureml.net , in cui sono elencati tutti i servizi Web (nuovi) e i servizi Web classici. Fare clic sul servizio Web cui si desidera accedere e fare clic sulla pagina **Consume (Uso)**. Copiare la **chiave primaria** per la proprietà **apiKey** e le **richieste batch** per la proprietà **mlEndpoint**.

![Servizi Web di Azure Machine Learning Studio (classico)](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-studio-classic-batch-execution-activity"></a>Attività di esecuzione batch Azure Machine Learning Studio (classica)

Il frammento di codice JSON seguente definisce un'attività di esecuzione batch Azure Machine Learning Studio (classica). La definizione dell'attività contiene un riferimento al servizio collegato Azure Machine Learning Studio (classico) creato in precedenza.

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

| Proprietà          | Descrizione                              | Obbligatoria |
| :---------------- | :--------------------------------------- | :------- |
| name              | Nome dell'attività nella pipeline     | Sì      |
| description       | Testo che descrive l'attività.  | No       |
| type              | Per Data Lake Analytics attività U-SQL, il tipo di attività è **AzureMLBatchExecution**. | Sì      |
| linkedServiceName | Servizi collegati al servizio collegato Azure Machine Learning Studio (classico). Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md). | Sì      |
| webServiceInputs  | Coppie chiave-valore, mapping dei nomi degli input del servizio Web Azure Machine Learning Studio (classico). La chiave deve corrispondere ai parametri di input definiti nel servizio Web Azure Machine Learning Studio pubblicato (classico). Il valore è una coppia di proprietà FilePath e dei servizi collegati di archiviazione di Azure che specifica i percorsi BLOB di input. | No       |
| webServiceOutputs | Coppie chiave-valore, mapping dei nomi degli output del servizio Web Azure Machine Learning Studio (classico). La chiave deve corrispondere ai parametri di output definiti nel servizio Web Azure Machine Learning Studio pubblicato (classico). Il valore è una coppia di proprietà FilePath e dei servizi collegati di archiviazione di Azure che specifica i percorsi BLOB di output. | No       |
| globalParameters  | Coppie chiave-valore da passare all'endpoint del servizio di esecuzione batch Azure Machine Learning Studio (classico). Le chiavi devono corrispondere ai nomi dei parametri del servizio Web definiti nel servizio Web Azure Machine Learning Studio pubblicato (classico). I valori vengono passati nella proprietà GlobalParameters della richiesta di esecuzione batch Azure Machine Learning Studio (classica) | No       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenario 1: Esperimenti di uso degli input/output del servizio Web che fanno riferimento ai dati presenti nell'archiviazione BLOB di Azure

In questo scenario, il servizio Web di Azure Machine Learning Studio (classico) esegue stime usando i dati di un file in un archivio BLOB di Azure e archivia i risultati della stima nell'archivio BLOB. Il frammento di codice JSON seguente definisce una pipeline di Data factory con un'attività AzureMLBatchExecution. Viene fatto riferimento ai dati di input e output nell'archiviazione BLOB di Azure tramite una coppia di LinkedName e FilePath. Nel servizio collegato di esempio di input e output sono diversi, è possibile usare diversi servizi collegati per ognuno degli input/output per Data Factory per poter selezionare i file corretti e inviare al servizio Web di Azure Machine Learning Studio (classico).

> [!IMPORTANT]
> Nell'esperimento di Azure Machine Learning Studio (classico), le porte di input e output del servizio Web e i parametri globali hanno nomi predefiniti ("INPUT1", "INPUT2") che è possibile personalizzare. I nomi scelti per le impostazioni webServiceInputs, webServiceOutputs e globalParameters devono corrispondere esattamente ai nomi negli esperimenti. È possibile visualizzare il payload della richiesta di esempio nella pagina della Guida esecuzione batch per l'endpoint Azure Machine Learning Studio (classico) per verificare il mapping previsto.


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
Un altro scenario comune durante la creazione di esperimenti di Azure Machine Learning Studio (classico) consiste nell'usare i moduli Import Data e output data. Il modulo Import Data consente di caricare i dati in un esperimento, mentre il modulo Output Data consente di salvare i dati derivanti dagli esperimenti. Per informazioni dettagliate sui moduli Import Data e Output Data, vedere gli argomenti [Import Data](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Output Data](https://msdn.microsoft.com/library/azure/dn905984.aspx) in MSDN Library.

Quando si usano i moduli Import Data e Output Data, è consigliabile usare un parametro del servizio Web per ogni proprietà di tali moduli. Questi parametri Web consentono di configurare i valori durante il runtime. Ad esempio, è possibile creare un esperimento con un modulo Import Data che usa un database SQL di Azure: XXX.database.windows.net. Dopo aver distribuito il servizio Web, si desidera consentire ai consumer del servizio Web di specificare un altro server SQL logico chiamato `YYY.database.windows.net` . È possibile usare un parametro del servizio Web per consentire la configurazione di questo valore.

> [!NOTE]
> L'input e l'output del servizio Web sono diversi dai parametri del servizio Web. Nel primo scenario è stato illustrato come è possibile specificare un input e un output per un servizio Web Azure Machine Learning Studio (classico). In questo scenario si passano i parametri per un servizio Web corrispondenti alle proprietà dei moduli Import Data e Output Data.

Viene preso in esame uno scenario relativo all'uso dei parametri del servizio Web. Si dispone di un servizio Web Azure Machine Learning Studio (classico) distribuito che usa un modulo Reader per leggere i dati da una delle origini dati supportate da Azure Machine Learning Studio (classico), ad esempio il database SQL di Azure. Dopo l'esecuzione batch, i risultati vengono scritti usando un modulo Writer (database SQL di Azure).  Negli esperimenti non sono definiti input e output del servizio Web. In questo caso, è consigliabile configurare i parametri del servizio Web rilevanti per i moduli Reader e Writer. Ciò consente la configurazione dei moduli Reader e Writer quando si usa l'attività AzureMLBatchExecution. Specificare i parametri del servizio Web nella sezione **globalParameters** del codice JSON dell'attività come indicato di seguito.

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

Al termine della ripetizione del training, aggiornare il servizio Web di assegnazione dei punteggi (esperimento predittivo esposto come servizio Web) con il modello appena sottoposto a training usando l' **attività di aggiornamento della risorsa di Azure Machine Learning Studio (classica)**. Per informazioni dettagliate, vedere l'articolo [Updating models using Update Resource Activity](update-machine-learning-models.md) (Aggiornamento dei modelli con Attività della risorsa di aggiornamento).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, che illustrano altre modalità di trasformazione dei dati:

* [Attività U-SQL](transform-data-using-data-lake-analytics.md)
* [Attività Hive](transform-data-using-hadoop-hive.md)
* [Attività Pig](transform-data-using-hadoop-pig.md)
* [Attività MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Attività di streaming di Hadoop](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)
* [Attività stored procedure](transform-data-using-stored-procedure.md)
