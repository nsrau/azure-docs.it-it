---
title: Rilevamento di MLflow per esperimenti Azure Databricks ML
titleSuffix: Azure Machine Learning
description: Configurare MLflow con Azure Machine Learning per registrare le metriche e gli artefatti da esperimenti Azure Databricks ml.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: c6bcf6f228049c4f5c4d1cd0d22cb69fb9677c1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91344334"
---
# <a name="track-azure-databricks-ml-experiments-with-mlflow-and-azure-machine-learning-preview"></a>Tenere traccia degli esperimenti Azure Databricks ML con MLflow e Azure Machine Learning (anteprima)

Questo articolo illustra come abilitare l'URI di rilevamento e l'API di registrazione di MLflow, collettivamente nota come [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), per connettere gli esperimenti di Azure DATABRICKS (ADB), MLflow e Azure Machine Learning.

[MLflow](https://www.mlflow.org) è una libreria open source per la gestione del ciclo di vita degli esperimenti di machine learning. Il rilevamento MLFlow è un componente di MLflow che registra e tiene traccia delle metriche di esecuzione del training e degli elementi del modello. Altre informazioni su [Azure Databricks e MLflow](https://docs.microsoft.com/azure/databricks/applications/mlflow/). 

Vedere [eseguire l'esperimento di tracking e creare endpoint con MLflow e Azure Machine Learning](how-to-use-mlflow.md) per altre integrazioni di MLflow e funzionalità Azure Machine Learning.

>[!NOTE]
> Come libreria open source, MLflow cambia di frequente. Di conseguenza, le funzionalità rese disponibili tramite l'integrazione Azure Machine Learning e MLflow devono essere considerate come anteprima e non sono completamente supportate da Microsoft.

> [!TIP]
> Le informazioni contenute in questo documento sono destinate principalmente a data scientist e sviluppatori che desiderano monitorare il processo di training del modello. Gli amministratori interessati al monitoraggio dell'uso delle risorse e degli eventi da Azure Machine Learning, come ad esempio quote, esecuzioni di training o distribuzioni del modello completate, possono consultare la sezione [Monitoraggio di Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Prerequisiti

* Installare il pacchetto `azureml-mlflow`. 
    * Questo pacchetto introduce automaticamente il `azureml-core` [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true), che fornisce la connettività per MLflow per accedere all'area di lavoro.
* Un' [area di lavoro Azure Databricks e un cluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).
* [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-azure-databricks-runs"></a>Rilevare le esecuzioni di Azure Databricks

Il rilevamento MLflow con Azure Machine Learning consente di archiviare le metriche e gli artefatti registrati dal Azure Databricks eseguito in entrambi i: 

* Area di lavoro di Azure Databricks.
* Area di lavoro di Azure Machine Learning

Dopo aver creato l'area di lavoro Azure Databricks e il cluster, 

1. Installare la libreria *azureml-mlflow* da pypi per assicurarsi che il cluster abbia accesso alle funzioni e alle classi necessarie.

1. Configurare il notebook dell'esperimento.

1. Connettere l'area di lavoro Azure Databricks e Azure Machine Learning area di lavoro.

Altre informazioni su questi passaggi sono disponibili nelle sezioni seguenti, in modo che sia possibile eseguire correttamente gli esperimenti MLflow con Azure Databricks. 

## <a name="install-libraries"></a>Installare le librerie

Per installare le librerie nel cluster, passare alla scheda **librerie** e selezionare **Installa nuovo**

 ![mlflow con Azure databricks](./media/how-to-use-mlflow-azure-databricks/azure-databricks-cluster-libraries.png)

Nel campo **pacchetto** Digitare azureml-mlflow e quindi selezionare Installa. Ripetere questo passaggio, se necessario, per installare altri pacchetti aggiuntivi nel cluster per l'esperimento.

 ![Libreria mlflow di installazione del database di Azure](./media/how-to-use-mlflow-azure-databricks/install-libraries.png)

## <a name="set-up-your-notebook"></a>Configurare il notebook 

Una volta configurato il cluster ADB, 
1. Selezionare **aree di lavoro** nel riquadro di spostamento a sinistra. 
1. Espandere il menu a discesa aree di lavoro e selezionare **Importa** .
1. Trascinare o individuare il notebook dell'esperimento per importare l'area di lavoro ADB.
1. Selezionare **Importa**. Il notebook dell'esperimento viene aperto automaticamente.
1. Sotto il titolo del notebook in alto a sinistra, selezionare il cluster che si vuole alleghi al notebook dell'esperimento. 

## <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Connettere le aree di lavoro di Azure Databricks e Azure Machine Learning

Il collegamento dell'area di lavoro ADB all'area di lavoro di Azure Machine Learning consente di tenere traccia dei dati dell'esperimento nell'area di lavoro Azure Machine Learning.

Per collegare l'area di lavoro ADB a un'area di lavoro Azure Machine Learning nuova o esistente, 
1. Accedere al [portale di Azure](https://ms.portal.azure.com).
1. Passare alla pagina **Panoramica** dell'area di lavoro ADB.
1. Selezionare il pulsante **collega Azure machine learning area di lavoro** in basso a destra. 

 ![Collegamento di aree di lavoro di database e Azure Machine Learning di Azure](./media/how-to-use-mlflow-azure-databricks/link-workspaces.png)

## <a name="mlflow-tracking-in-your-workspaces"></a>Rilevamento MLflow nelle aree di lavoro

Dopo aver creato un'istanza dell'area di lavoro, il rilevamento MLflow viene impostato automaticamente in modo da essere rilevato in tutte le posizioni seguenti:

* Area di lavoro Azure Machine Learning collegata.
* L'area di lavoro ADB originale. 

Tutti gli esperimenti atterrano nel servizio di rilevamento Azure Machine Learning gestito.

Il codice seguente deve trovarsi nel notebook dell'esperimento per ottenere l'area di lavoro Azure Machine Learning collegata. 

Questo codice, 

*  Ottiene i dettagli della sottoscrizione di Azure per creare un'istanza dell'area di lavoro Azure Machine Learning. 

* Si presuppone che siano presenti un gruppo di risorse e un'area di lavoro Azure Machine Learning. in caso contrario, è possibile [crearli](how-to-manage-workspace.md). 

* Imposta il nome dell'esperimento. L'oggetto `user_name` è coerente con l'oggetto `user_name` associato all'area di lavoro Azure Databricks.

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

#Set MLflow experiment. 
experimentName = "/Users/{user_name}/{experiment_folder}/{experiment_name}" 
mlflow.set_experiment(experimentName) 

```

### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>Impostare il rilevamento di MLflow per tenere traccia solo nell'area di lavoro Azure Machine Learning

Se si preferisce gestire gli esperimenti rilevati in una posizione centralizzata, è possibile impostare il rilevamento MLflow per tenere traccia **solo** nell'area di lavoro Azure Machine Learning. 

Includere il codice seguente nello script:

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Nello script di training, importare `mlflow` per usare le API di registrazione MLflow e avviare la registrazione delle metriche di esecuzione. L'esempio seguente registra la metrica di perdita per il periodo. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="register-models-with-mlflow"></a>Registrare i modelli con MLflow

Dopo aver eseguito il training del modello, è possibile registrare e registrare i modelli nel server di rilevamento back-end con il `mlflow.<model_flavor>.log_model()` metodo. `<model_flavor>`, fa riferimento al Framework associato al modello. [Informazioni sulle versioni del modello supportate](https://mlflow.org/docs/latest/models.html#model-api). 

Il server di rilevamento back-end è l'area di lavoro Azure Databricks per impostazione predefinita. a meno che non si sia scelto di [impostare MLflow Tracking per tenere traccia solo nell'area di lavoro Azure Machine Learning](#set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace), il server di rilevamento back-end è l'area di lavoro Azure Machine Learning.   

* **Se non esiste un modello registrato con il nome**, il metodo registra un nuovo modello, crea la versione 1 e restituisce un oggetto ModelVersion MLflow. 

* **Se esiste già un modello registrato con il nome**, il metodo crea una nuova versione del modello e restituisce l'oggetto versione. 

```python
mlflow.spark.log_model(model, artifact_path = "model", 
                       registered_model_name = 'model_name')  

mlflow.sklearn.log_model(model, artifact_path = "model", 
                         registered_model_name = 'model_name') 
```

## <a name="create-endpoints-for-mlflow-models"></a>Creare endpoint per i modelli MLflow

Quando si è pronti per creare un endpoint per i modelli ML. È possibile distribuire come, 

* Un Azure Machine Learning Request-Response servizio Web per il punteggio interattivo. Questa distribuzione consente di sfruttare e applicare le funzionalità di gestione del modello di Azure Machine Learning e di rilevamento della tendenza ai dati nei modelli di produzione. 

* Oggetti modello MLFlow, che possono essere usati nelle pipeline di streaming o batch come funzioni Python o funzioni definite dall'utente Pandas nell'area di lavoro Azure Databricks.

### <a name="deploy-models-to-azure-machine-learning-endpoints"></a>Distribuire modelli a endpoint Azure Machine Learning 
È possibile utilizzare l'API [mlflow. azureml. deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) per distribuire un modello nell'area di lavoro Azure Machine Learning. Se il modello è stato registrato solo nell'area di lavoro Azure Databricks, come descritto nella sezione [registrare i modelli con MLflow](#register-models-with-mlflow) , specificare il `model_name` parametro per registrare il modello nell'area di lavoro Azure Machine Learning. 

È possibile distribuire Azure Databricks esecuzioni negli endpoint seguenti: 
* [Istanza di contenitore di Azure](how-to-use-mlflow.md#deploy-to-aci)
* [Servizio Azure Kubernetes](how-to-use-mlflow.md#deploy-to-aks)

### <a name="deploy-models-to-adb-endpoints-for-batch-scoring"></a>Distribuire i modelli negli endpoint ADB per il Punteggio batch 

È possibile scegliere Azure Databricks cluster per il Punteggio batch. Il modello MLFlow viene caricato e usato come funzione definita dall'utente di Spark Pandas per assegnare un punteggio ai nuovi dati. 

```python
from pyspark.sql.types import ArrayType, FloatType 

model_uri = "runs:/"+last_run_id+ {model_path} 

#Create a Spark UDF for the MLFlow model 

pyfunc_udf = mlflow.pyfunc.spark_udf(spark, model_uri) 

#Load Scoring Data into Spark Dataframe 

scoreDf = spark.table({table_name}).where({required_conditions}) 


#Make Prediction 

preds = (scoreDf 

           .withColumn('target_column_name', pyfunc_udf('Input_column1', 'Input_column2', ' Input_column3', …)) 

        ) 

display(preds) 
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si prevede di usare le metriche registrate e gli artefatti nell'area di lavoro, la possibilità di eliminarli singolarmente non è attualmente disponibile. Eliminare invece il gruppo di risorse che contiene l'account di archiviazione e l'area di lavoro, in modo che non vengano addebitati costi:

1. Nel portale di Azure fare clic su **Gruppi di risorse** all'estrema sinistra.

   ![Eseguire l'eliminazione nel portale di Azure](./media/how-to-use-mlflow-azure-databricks/delete-resources.png)

1. Nell'elenco selezionare il gruppo di risorse creato.

1. Selezionare **Elimina gruppo di risorse**.

1. Immettere il nome del gruppo di risorse. Selezionare **Elimina**.

## <a name="example-notebooks"></a>Notebook di esempio

I [MLflow con Azure Machine Learning notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) dimostrano ed espandono i concetti presentati in questo articolo.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i modelli](concept-model-management-and-deployment.md).
* [Verificare l'esecuzione dell'esperimento e creare endpoint con MLflow e Azure Machine Learning](how-to-use-mlflow.md). 
* Altre informazioni su [Azure Databricks e MLflow](https://docs.microsoft.com/azure/databricks/applications/mlflow/).
